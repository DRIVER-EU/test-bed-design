<!-- {
  "backlink": "index.html#fig1.1.1",
  "level":"1.1",
  "list_caption":"Figure: PTME paradigm applied to DRIVER+",
  "alt":"PTME paradigm applied to DRIVER+",
  "nro":1,
  "url":"img/pmte_paradigm.png",
  "index":1,
  "caption_template":"Figure _BOOK_IMAGE_NUMBER_. _CAPTION_.",
  "label":"PTME paradigm applied to DRIVER+",
  "attributes":{},
  "skip":false,
  "key":"1.1.1"
} -->

# List of Figures

{% for picture in book.pictures %}
  {{ picture | dump }}
  1. [{{ picture.alt }}]({{ picture.backlink }})
{% endfor %}