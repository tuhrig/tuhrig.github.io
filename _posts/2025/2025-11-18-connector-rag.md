---
layout: post
title: "Connector-Based RAG With Live Confluence Data"
date: "2025-11-18"
categories:
  - "tech"
  - "AI"
---

In my last post ([How to Build Your Own Local RAG System](https://tuhrig.de/local-rag)) I showed how to build a fully local Retrieval-Augmented Generation (RAG) setup using **pre-processed data**.
That approach works well, but it also has a downside:
**Your data becomes stale unless you regularly rebuild your embeddings.**
Here, I want to show a completely different approach, for enhancing your LLM with your very own [Confluence](https://www.atlassian.com/de/software/confluence) data.

## A Connector-Based RAG System

Instead of preparing data upfront, we simply fetch it **ad-hoc** from Confluence using the official REST API.

1.  User asks a question
2.  The LLM generates search keywords from the question
3.  A Java client queries Confluence via its REST-API
4.  Matching pages are fetched
5.  Their content becomes the RAG context

Just a connector → Confluence → the LLM. 
It’s a “just-in-time RAG” without any preprocessing step.

## 1. `ConfluenceClient.java`

First, we need to build our way to read Confluence.
So this class will do two things:

- Perform a [CQL](https://developer.atlassian.com/server/confluence/advanced-searching-using-cql/) (**C**onfluence **Q**uery **L**anguage) search
- Fetch a page’s content (`HTML`), which is then stripped and cleaned to plain text

Below is a shortened version of the code.
In the end, it's just a Java client which calls a REST-API.
To do so, Confluence offers the so-called [personal access token](https://confluence.atlassian.com/enterprise/using-personal-access-tokens-1026032365.html).

``` java
public class ConfluenceClient {

  private final String baseUrl = "https://my-confluence.de/confluence";
  private final String pat = "PERSONAL-ACCESS-TOKEN";

  public JsonNode search(String query) {
    String cql = buildCql(query);
    String url = baseUrl + "/rest/api/search?cql=" + encode(cql);
    HttpRequest req = request(url);
    String body = send(req);
    return MAPPER.readTree(body);
  }

  public JsonNode getPage(String id) {
    var url = baseUrl + "/rest/api/content/" + id + "?expand=body.storage";
    var req = request(url);
    var body = send(req);
    return MAPPER.readTree(body);
  }

  private String buildCql(String query) {
    // Example output:
    //   space in ("MY-SPACE","YOUR-SPACE") AND type = "page" AND (text ~ "animal" OR text ~ "human")
    ...
  }

  private HttpRequest request(String url) { ... }
  private String send(HttpRequest req) { ... }
}

```

# 2. LLM Generates Search Keywords

But before we even hit Confluence, the LLM produces 3–5 high-quality keywords from the user’s question.
This step avoids sending full questions with stopwords to the Confluence search API.

``` java
public String[] extractSearchKeywords(String question) {

  var systemPrompt = """
      You get a question related to our internal Confluence.
      Produce 3-5 relevant search terms.
      No stopwords, no sentences, only domain terminology.
      Return a comma-separated list.
      """;

  var response = callOpenAI(systemPrompt, question);
  return response
      .toLowerCase()
      .replaceAll("[^a-z0-9öäüß, ]", " ")
      .split("[, ]+");
}
```

# 3. Putting it all together: `answerQuestion()`

Now we can put it all together:

- We have implemented a simple Java client to read Confluence
- We use the LLM to convert the user's questions to search keywords
- Now we can query Confluence and build a RAG context with its search results

``` java
public String answerQuestion(String question) throws Exception {

  // 1) Let the LLM decide which keywords to search for
  String[] keywords = extractSearchKeywords(question);
  String keywordQuery = String.join(" ", keywords);

  // 2) Search Confluence
  JsonNode results = confluence.search(keywordQuery).path("results");

  if (results.isEmpty()) {
    return "No relevant Confluence data found.";
  }

  // 3) Build context by loading up to 10 pages
  StringBuilder ctx = new StringBuilder();
  for (JsonNode r : results) {
    String id = r.path("content").path("id").asText();
    JsonNode page = confluence.getPage(id);

    String html = page.path("body").path("storage").path("value").asText();
    String text = stripHtml(html);

    ctx.append("=== ").append(page.path("title").asText()).append(" ===\n");
    ctx.append(text).append("\n\n");
  }

  // 4) Ask the LLM based on this context
  String systemPrompt = """
      You answer strictly based on the Confluence context.
      If the answer is not in the context, say:
      "The Confluence data does not contain the answer."
      """;

  String userPrompt = """
      QUESTION:
      %s

      CONTEXT:
      %s
      """.formatted(question, ctx.toString());

  return callOpenAI(systemPrompt, userPrompt);
}
```

# 4. Main class to run it

Now we can go ahead and ask a question:

``` java
public class Main {
  public static void main(String[] args) throws Exception {
    AiAgent agent = new AiAgent();
    String answer = agent.answerQuestion("What is a report?");
    System.out.println(answer);
  }
}
```

## The Magic: No Vector Store Needed

This approach is super simple:

- The LLM itself creates the search query
- Confluence returns only relevant pages
- The pages become the RAG context
- The LLM answers based on that context

## ✅ Advantages

- Easy to build:
No embedding pipeline, no vector database, no chunking logic.
- Always up to date:
The answers always use live Confluence data.
- Minimal infrastructure:
Just Java + HTTP + your LLM endpoint.
Great for enterprise environments:
Works even if you cannot store data locally due to compliance restrictions.

## ❌ Disadvantages

- You are limited by the Confluence search API:
CQL quality determines response quality.
If CQL can’t find it, the LLM can’t answer it.
- Potentially slow for large queries:
Each request triggers multiple Confluence API calls.
- No semantic search:
You rely on keyword-based text search, not embeddings.
- Rate limits:
Large teams or heavy usage may hit Confluence rate limits.

## When should you use this approach?

### Use connector-based RAG when you want:

- fast development
- zero data preprocessing
- live data from Confluence
- minimal maintenance

### Use local vector-based RAG (as in my previous post) when:

- you need semantic search
- you want to customize chunking, scoring, ranking
- you want low latency and high throughput
- you need strong control over your retrieval logic

**Best regards,**  
Thomas