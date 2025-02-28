---
title : "Explore OpenSearch (Optional)"
date : "2025-02-21"
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---
OpenSearch offers a powerful set of features for searching and retrieving data. This section explores its query API to showcase various capabilities.

In OpenSearch, a schema—referred to as mapping—defines how data is structured and indexed. The mapping determines how fields in JSON documents are analyzed and made searchable. While OpenSearch includes automatic schema detection for quick deployment, manually defining the schema during index creation is often the best approach. Although OPEA and the OpenSearch microservice have already configured the mapping, it can still be retrieved for the embeddings index using the following command.

curl -XGET https://localhost:9200/rag-opensearch/_mapping --insecure -u admin:strongOpea0! | jq .

You should see a response like this:

![VPC](/images/5.fwd/image093.png)

The rag-opensearch index consists of three fields: metadata, text, and vector_field. The metadata field stores nested JSON, including a source field, which can be accessed in queries using dot notation (e.g., metadata.source). Both metadata.source and text are defined as text type fields with a keyword subfield. Text fields undergo parsing and term analysis to generate tokens for matching, while keyword fields are normalized and used for exact-match queries. The vector_field is a knn_vector type field designed to store vectors with 768 dimensions. The storage engine employed is the Non-Metric Space Library (NMSLIB), utilizing the Hidden Navigable Small Worlds (HNSW) algorithm.

**Lexical queries**

OpenSearch is a lexical search engine in addition to being a vector search engine. You can query the rag content using text queries. Use the below query to search the OpenSearch documents, with its default TF/IDF ranking algorithm (see also Okapi BM25, text-based ranking ).

{{% notice info %}}
If your Cloud Shell has terminated, you may need to reestablish port forwarding to the OpenSearch microservice. See the previous module for instructions.
{{% /notice %}}

Execute the following command to run the query

![VPC](/images/5.fwd/image094.png)

The first line of the command runs the curl command, with the URL containing the endpoint (localhost:9200, forwarded to the OpenSearch microservice) and the API specification. Here, the request is directed to the rag-opensearch index and calls the _search API. The following lines define the TLS and authentication parameters, and after the -d flag, the request body specifies the query.

This query uses a simple_query_string search for the text "What is Nike's 2023 revenue?". The simple_query_string function analyzes the input text, breaking it down into individual tokens—technically referred to as terms—and matches them against the text field in all documents within the index. It then scores and ranks the results based on the TF/IDF algorithm to surface the most relevant document at the top.

Additional directives in the query instruct OpenSearch to exclude all fields from the response ("_source": false—removing this would return the original document values), limit the results to a single match ("size": 1), and highlight matching terms in the text field ("highlight": ...).

![VPC](/images/5.fwd/image095.png)

OpenSearch's response begins with a metadata section that includes details such as the query processing time on the server side (8 ms in this case), whether the query timed out, and information about the responding shards. This is followed by the hits section, which contains the total number of matches, the highest relevance score, and the matched documents themselves. Each document includes the index it belongs to (_index), its unique identifier (_id), its relevance score, the source fields (which were excluded in this query), and highlighted snippets indicating where the query terms matched the document. The highlights use HTML <em> tags to emphasize the matching terms.

However, the response isn't ideal. While it correctly identifies mentions of Nike, it also includes irrelevant words like what and is, reducing the precision of the results.

You can experiment with different query terms by modifying the "query" field (e.g., replacing "what is nike 2023 revenue?" with other phrases) to observe how the responses change. Adjusting the "size" parameter to 2 or more allows you to see multiple results at once. Try queries like "workplace policies", "footwear apparel", "men sales", or "women sales" to explore different outputs.

To refine the search, you can run an exact k-Nearest-Neighbor (k-NN) query. First, retrieve the embedding for your query using the chatqna-tei microservice. The microservice returns an array of arrays, but only the inner array is needed for the OpenSearch query. The jq command extracts the first element and assigns it to the $embedding variable.

To execute the command, you’ll need the port mapped to the tei microservice. Use the ps aux | grep kubectl command to check running processes and their assigned ports. If you've followed the guide correctly, the chatqna-tei microservice should be running on port 9800.

![VPC](/images/5.fwd/image096.png)

You can use echo $embedding to see the generated embedding. Now you'll create the local file query.json with the embeding merged into the query, and then run an exact k-Nearest-Neighbors (k-NN) query to compare the query embeddingg to every document (chunk) in the index and retrieve the closest matches

![VPC](/images/5.fwd/image097.png)

This query is a script_score query, employing a saved script to do k-NN score calculation, comparing the query vector to every document in the index. The script_score query includes a sub-query, which you can use to apply filters to non-vector fields. ChatQnA just sends the file key in the metadata.source field, so this query just uses a match_all, which matches every document in the index. The script portion of the query specifies the knn_score script, with parameters that tell the script which field has the vector embedding for the doc, passes the embedding as the query_value and specifies l2 as the distance metric (space_type).

This response is correct. The first result includes the text "NIKE, Inc. Revenues were $51.2 billion in fiscal 2023." Unlike traditional text-based queries, highlighting is not supported for vector fields because they do not contain raw source text. As a result, the retrieved content appears as-is from the text field.

Exact k-Nearest-Neighbor (k-NN) search is highly effective when dealing with a relatively small number of documents. However, as the dataset expands, query latency increases significantly. Beyond a few hundred thousand documents, exact k-NN search becomes impractically slow. To handle larger datasets efficiently, approximate nearest neighbor (ANN) search is a better alternative. The command below utilizes the Hierarchical Navigable Small World (HNSW) algorithm to find the closest matches.

![VPC](/images/5.fwd/image098.png)

This query is a knn query, which uses the algorithm you specified in the field mapping to determine the nearest neighbors. You just pass in a vector and a value for k (the count of neighbors to retrieve), and opensearch does the rest.

**Hybrid search with OpenSearch**

Again, you can see the correct document is the first result retrieved. Using approximate k-NN you can scale your OpenSearch vector database to billions of vectors and 1000s of queries per second.

OpenSearch supports hybrid search  -- where you specify both a lexical and vector query, along with a normalization and merge strategy. OpenSearch runs both queries normalizes and merges the results. When you perform hybrid search, you set a Search Pipeline , and send queries through that pipeline. Use the below command to use OpenSearch's REST API to set a search pipeline.

![VPC](/images/5.fwd/image099.png)

This pipeline uses min/max normalization  to set all of the lexical and vector scores in the range [0, 1]. It uses the arithmetic mean to combine the scores, with a weight of 0.3 for the first query clause and 0.7 for the second query clause. Note, this is not a query itself, when you send queries to this search pipeline, OpenSearch applies the weights. The phase_results_processor is a flexible, generic construct - the query clauses can be either lexical or vector.

To use the pipeline, you send a query to the pipeline API. Use the below command to create the query in the file hybrid_query.json.

![VPC](/images/5.fwd/image100.png)

This hybrid query contains two sub-queries - a lexical query for "footwear revenue" and a vector query with an embedding representing "What is Nike 2023 revenue?". The value for k, 2, ensures that the results will contain at most two vector matches.

The curl command sends this query to the nlp-search-pipeline you just defined. You should get these results (we've removed the result metadata and other portions to show the relevant output)

The first match in the results is identical to the one retrieved using the approximate nearest-neighbor query. The second and fourth matches originate from the lexical query "footwear revenue," while the second result comes from the vector search. This approach effectively blends abstract, semantic insights about revenue with more concrete keyword-based matches for "footwear revenue."

When designing search systems, predicting the exact types of queries users will run can be challenging. By leveraging hybrid queries like this, you can provide a balanced mix of both semantic and lexical results, enhancing search accuracy and relevance.

You can also experiment with the weight distribution in the nlp-search-pipeline to observe its impact on result ranking. For instance, setting the lexical query weight to 0.9 and the vector search weight to 0.1 shifts the top result to "Footwear revenues increased 25% on a currency-neutral basis,..." with a score of 0.9. Meanwhile, the previous top result, "In fiscal 2023, NIKE, Inc. achieved record revenues of $51.2 billion," moves to the second position with a score of 0.1, followed by all other vector matches.

**Conclusion**

Through this process, you have executed and compared different search techniques, including lexical search, exact k-NN, approximate k-NN, and hybrid search, directly using OpenSearch’s APIs. Currently, OPEA relies solely on approximate k-NN, but future enhancements may introduce these advanced hybrid search techniques!
