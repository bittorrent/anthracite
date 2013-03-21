## Anthracite event manager ##

* what: track and manage all changes and events that can have a business and/or operational impact.
* why: to increase operational visibility and collaboration

some use cases:

* changelog for troubleshooting and keeping people informed
* enriching monitoring dashboards with markers and annotation text
* aiding with visual and numerical analysis of events that affect performance (MTTD, MTTR, etc)

## Design goals ##

* do one thing and do it well.  aim for simplicity, flexibility and integration.
* deliver events in various formats and support querying for tags and text (full text search)
* support arbitrary tags, allow events with multiple lines, even rich text and hyperlinks.

![Screenshot](https://raw.github.com/Dieterbe/anthracite/master/screenshots/screenshot.png)

## Components ##

* anthracite-web.py is the web app (interface for humans, and HTTP POST event receiver)
* anthracite-compose-submit.sh to interactively compose and submit events from the CLI.
* anthracite-tcp-receiver.py is the TCP receiver (only supports single-line events!)
* an sqlite database is automatically created, it suffices.


## Methods of submitting events ##

* HTTP POST receiver in the web app (so you can use something like curl, see source of anthracite-compose-submit.sh)
* manually, in the web interface
* manually, with the `anthracite-compose-submit.sh` CLI script
* `anthracite-tcp-receiver.py` on TCP port 2005 (for one line events) in `<unix timestamp> [<tag1>[ <tag2>[...]]] -- <description>` format


## Integration ##

* [Timeserieswidget](https://github.com/Dieterbe/timeserieswidget) shows graphite graphs with anthracite's events
* The [Graph-Explorer graphite dashboard](https://github.com/Dieterbe/graph-explorer) uses that.
* At Vimeo we submit deploy events using curl

![Screenshot2](https://raw.github.com/Dieterbe/anthracite/master/screenshots/flot-annotated-event.png)

## Dependencies ##

* python2
* python2-pysqlite
* twisted (only needed for the TCP receiver)


## Installation ##

should be super easy...

* install dependencies
* clone repo
* there is no third step. you're ready.


## About "relevant events" ##

I recommend you submit any event that **has** or **might have** a **relevant** effect on:
* your application behavior
* monitoring itself (for example you fixed a bug in metrics reporting. it shouldn't look like the app behavior changed)
* the business (press coverage, viral videos, etc), because this also affects your app usage and metrics.


## Formats and conventions ##

The format is very loose.  I recommend to use tags for categorisation, and ultimately there'll be full-text search so you don't have to worry too much
about formatting or additional tags, as long as the info is within the event.

However, I recommend to try to use some "standardized" nomenclature, such as 'deploy', 'manual' (for manual changes), 'incident', 'outage', 'resolved', ...
You can use tags like `author=<person>` but this usually doesn't give any benefit over just tagging `<person>`.


## TODO ##

* plugins for puppet, chef to automatically submit their relevant events (or logstash filter to create anthracite events from logs)
* make web UI table use colors to denote outages according to their severity
* auto-update events on web interface to make semi-realtime
* on graphs in dashboards, show timeframs from start to end, and start to "cause found", to "resolved" etc.
* implement automatic statistics on events and analysis such as TTD, TTR, with colors for severity levels etc
* concurrent webserver to make sure all http requests can get served

