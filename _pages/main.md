---
layout: single
permalink: /
author_profile: true

---


<div class="grid__homepage1" >
    <div style="background: rgb(19, 53, 50); border-radius: 20px; border: solid 1px rgb(143, 249, 241); padding:5px 25px 5px 25px; margin-right:5px; text-align: center; display: flex; justify-content: center; flex-direction: column;">
        <p class="archive__item-excerpt" style="margin-bottom: 10px; font-weight: 700; font-size: 20px;">Hello!</p>
        <p style="line-height: 1.3; margin-bottom: 0px">
        To learn more about the blog, check out the <a href="{{ site.baseurl }}about" style="color:lightgray;">About</a> page. Before using code, please review the <a href="{{ site.baseurl }}disclaimer" style="color:lightgray;">disclaimer</a>.✌️
        </p>
    </div>
  <div style="display: flex; flex-direction: column; justify-content: center">
    <div style="background: rgb(69, 51, 56);border-radius: 20px; padding:15px 25px 15px 25px; margin-left:5px;margin-bottom:15px;margin-top:2px;">
        <center>
            <p class="archive__item-excerpt" style="margin: 0px;">
            <i class="fab fa-youtube"></i> <a href="https://www.youtube.com/channel/UCSMxuZP6KUb_i9F-K1LAtrw" style="color:lightgray;">YouTube</a>
            </p>
        </center>
    </div>
        <div style="background: rgb(27, 29, 34);border-radius: 20px; padding:15px 25px 15px 25px; margin-left:5px;">
        <center>
            <p class="archive__item-excerpt" style="margin: 0px;">
                <i class="fab fa-github"></i> <a href="https://github.com/krasscodes" style="color:lightgray;">GitHub</a>
            </p>
        </center>
    </div>
  </div>
</div>

<h1> Recent posts </h1>

<div style="padding:20px; background: rgb(27, 29, 34);border-radius: 20px;">
<ul>
{% for category in site.categories %}
  <li><a name="{{ category | first }}">{{ category | first }}</a>
    <ul>
    {% for post in category.last %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
    </ul>
  </li>
{% endfor %}
</ul> 
</div>
