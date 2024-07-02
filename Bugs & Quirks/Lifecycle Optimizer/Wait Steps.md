# Wait Steps

## 'Ghosts' in LOs
After removing 'Wait' actions, users from that step will still wait their original wait period before moving on/exiting.

During this wait period they are invisible in the UI and API. Support may have limited visibility as well.

**Solution:** The only solution is to set the LO to 'inactive' which fully removes everyone from the LO, then switch back to 'active'. 

## Wait Times
Wait times are actually a range between `n-1` to `n` minutes. So a 1 minute wait is actually 0-1 minutes, 2 minute wait is 1-2 minutes, and so on. 
This may be relevant when setting a custom field value then using a 1 minute delay. This 1 minute delay could trigger instantly in 0-1 seconds, before the value has had a chance to fully save. This is especially the case when using triggers to set custom profiles vars, which use the same time variable time ranges.
