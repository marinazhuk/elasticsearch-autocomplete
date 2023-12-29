# Elasticsearch Autocomplete with leveraging typos and errors

* Create ES index that will serve autocomplete needs with leveraging typos and errors (max 3 typos if word length is bigger than 7)

## Create index

### Mapping
```shell
curl --request PUT localhost:9200/dictionary -H 'Content-Type: application/json' -d '
{
    "settings" : {
      "analysis" : {
        "analyzer" : {
          "ngram_analyzer" : {
            "tokenizer" : "ngram_tokenizer"
          }
        },
        "tokenizer" : {
          "ngram_tokenizer" : {
            "type" : "ngram",
            "min_gram" : "1",
            "max_gram" : "2"
          }
        }
      }
    },
  
    "mappings": {
    "properties": {
      "word": {
        "type": "text",
        "analyzer": "ngram_analyzer"
      }
    }
  }
}'
```


### Import word list to index:

```shell
curl --request PUT localhost:9200/dictionary/_bulk -H "Content-Type: application/json" --data-binary @wordlist.json
```



### Search Query 

```json
{
  "query": {
    "match": {
      "word": {
        "query": "abacus",
        "minimum_should_match": "13<-9"
      }
    }
  }
}
```
* 13 clauses is an amount of combination for word length 7
* 9 maximum number of clauses that will not be met for 3 typos


## Result examples

### 6 letters word 

* ### Direct search
![direct.png](screenshots%2Fless_and_equal_7%2Fdirect.png)
* ### Autocomplete
![autocomplete.png](screenshots%2Fless_and_equal_7%2Fautocomplete.png)
* ### Typo
![typo.png](screenshots%2Fless_and_equal_7%2Ftypo.png)


### 8 letters word
* ### Direct search
![direct.png](screenshots%2Fbigger%20than%207%2Fdirect.png)
* ### Autocomplete
![autocomplete.png](screenshots%2Fbigger%20than%207%2Fautocomplete.png)
* ### 3 typos
![3_typos.png](screenshots%2Fbigger%20than%207%2F3_typos.png)
* ### 4 typos
![4_typos.png](screenshots%2Fbigger%20than%207%2F4_typos.png)