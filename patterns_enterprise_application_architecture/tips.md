# TIPS

## KEEP ALL DIFFERNT LAYERS AND PLAYERS IN PROCESS

hould try to keep all the code in a single process, either on one node or copied on several nodes in a cluster. Don't try to separate the layers into discrete processes unless you absolutely have to. Doing that will both degrade performance and add complexity.

=> REALLY??