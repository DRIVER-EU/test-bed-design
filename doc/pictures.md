# List of Figures

{{ book.pictures | dump }}

  {% for picture in book.pictures %}
    {{ picture | dump  }}
    1. [{{ picture.list_caption }}]({{ picture.backlink }})
  {% endfor %}