# List of Figures

{% for picture in book.pictures %}
  1. [{{ picture.alt }}]({{ picture.backlink }})
{% endfor %}