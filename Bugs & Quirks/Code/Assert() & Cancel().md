# assert()
It's not [explicitly documented](https://getstarted.sailthru.com/developers/zephyr-functions-library/assert/), but assert() and cancel() **DO NOT** stop LO Flows from running. Further steps will still execute.

[See here for adding a custom Event Log with assert()/cancel()](https://github.com/Colin-Whelan/SailthruCommunityDocs/blob/main/README.md#event-log-for-messages-canceled-with-assert--cancel).