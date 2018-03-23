# List of Figures

{{ book.pictures }}

  {% for picture in book.pictures %}
    {{ picture }}
    1. [{{ picture.list_caption }}]({{ picture.backlink }})
  {% endfor %}