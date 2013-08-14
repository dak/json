## Reasons to Use JSON (or XML)

1.  JSON is more compact, meaning smaller file sizes to send to a client
    XML: The webserver sends GZIP'd documents and my guess is that there is minimal overhead since regexp's can convert the JSON doc to HTML and back.
2.  JSON is [formal and precise](http://json.org/), so there's less possibility of ambiguity or exceptions
    XML: So is XML
3.  JSON is **much** faster to parse than XML, and you don't need to write any custom code to parse it
    XML: Parsing is just as easy. For both JSON and XML the difficulty is in Validating, not parsing.
    XML: In every use case of CollXML, the exact same HTML must be used (PDFs, EPUB, online, editor, transfer between services) so no need to reserialize/revalidate
    1.  Current code to parse a book (already broken with new COLLXML format, and code complexity increases exponentially with more features):
          `````
          parseHTML = (html) ->
            if typeof html isnt 'string' then return []

            results = []

            $(html).find('> ol').find('> li').each (index, el) ->
              $el = $(el)
              $node = $el.children().eq(0)

              if $node.is('a')
                id = $node.attr('href')
                title = $node.text()

              # Only remember the title if it's overridden
              if not title or $node.hasClass('autogenerated-text')
                results.push({id: id})
              else
                results.push({id: id, title: title})

            return results
          `````
    XML: Serializing JSON to HTML is equally "complex"

    2.  No code is required to parse JSON, since support is built-in to Backbone, but if you needed to, it would always be one line:
        XML: It's also 1 line for XML. I believe validating, not parsing is the challenging part.
        1.  ```JSON.parse(string)``` in JavaScript
        XML: jQuery(html)
        2.  In Python, just ```import json``` and then ```json.loads(string)```
        XML: import lxml; lxml.parse(xmlStr)

3.  JSON, unlike XML, matches the data model of most programming languages
    1.  Literally "JavaScript Object Notation", it can be serialized and parsed back into an object in any modern browser
    2.  Maps easily to Python as well: Object = Dictionary, Array = List, etc.
4.  Support for JSON has [only been growing](http://blog.appfog.com/why-json-will-continue-to-push-xml-out-of-the-picture/) as it is becoming the default data exchange format for the web
5.  PostgreSQL has built-in support for JSON, including automatic validation
    XML: PostgreSQL also has built-in support for XML longer than JSON https://wiki.postgresql.org/wiki/XML_Support with support for complex queries (XPath), conversion (XSLT), and validation (Relax-NG)
    1.  http://www.postgresql.org/docs/devel/static/datatype-json.html
    2.  http://www.postgresql.org/docs/9.3/static/functions-json.html
6.  [Backbone.js](http://backbonejs.org) is designed to work with specifically with JSON
    XML: Backbone is **not** designed to work with Objects as attributes (trees) (change handlers don't fire when you change a child in the tree)
    1.  "Backbone.js gives structure to web applications ... and connects it all to your existing API over a RESTful **JSON** interface."
7.  An XML or HTML markup of a book can be generated from data more easily and quickly than XML can be parsed into data
XML: Examples? By converting once, when editing, you only need to validate once and can reuse the same XML in every use case we need
8.  A template (or multiple templates) could be used to easily convert data into any format, simplifying support for different formats.  So, for example, we should be able to avoid restyling every book.
XML: All of the formats (use cases) are HTML and styling is done in CSS
9.  Because of things like xincludes, XML must be parsed into data, manipulated, and then re-generated.  Storing as JSON skips the first step, because it already _is_ data.
XML: Parsing the data is $xml.find('.xinclude').each ($node) => jQuery.get($node.attr('href')). Doing this in JSON+python/JS is at least as difficult.

10. We're **already** using JSON.  Currently, the XML/HTML is simply being served as a JSON field.  Serving just JSON would simplify it to a single data format.
XML: We're **already** using XML for all of our content

11. Folders are already serving their contents as JSON.
    Folders are never published or used by anything other than ATC so I don't see a need for them to be HTML.

12. ATC is effectively just taking the HTML/XML being served currently and converting it into a JavaScript Object (JSON).
XML: And if it's stored as JSON, every single other service will need to convert the JSON back to XML (PDF, EPUB, Web View)
