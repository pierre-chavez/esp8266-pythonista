<html>
<head>
<title>esp8266-pythonista</title>
</head>
<body>

<h1>esp8266 y Pythonista</h1>
<p>proximamente veremos como comunicar la placa esp8266 con pythonista.</p>
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

[Link to a post]({{ site.baseurl }}{% link _posts/2018-03-04-hola-mundo.md %})

</body>
</html>
