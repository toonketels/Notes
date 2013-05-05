# QUESTIONS

## HOW TO SEPARATE FUNCTIONALITY INTO DIFFERENT APPLICATION LAYERS?

1. Know that most should live in domain.
2. To separate between presentation and domain ask: "If I had to
   implement an alternative UI (think CLI/browser), which code would
   have been duplicated?". Put this code into the domain.
3. To separate between domain and data source ask: "If I had to swap
   data source (xml/mysql/mongo/elastic search), which code should I
   have to write?". This code is probably functionality which exists
   in your current data source which belongs in the domain.