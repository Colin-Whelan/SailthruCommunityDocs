# Custom Events
Sends API events to initiate LOs and triggers.

Uses: POST - event
vars are optional and can be completely omitted if not used.
``` handlebars
{
  "id" : "email_address",
  "event"  : "event_name",
  "vars" : {
    "url" : "http://example.com/123",
    "name" : "Widget"
  }
}
```