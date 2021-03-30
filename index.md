---
layout: default
permalink: /
title: Block 12
---

<div class="row">
    <div class="col s12 bg-dark-gray-upper center-align">
        <h3 class="logo-text">Games</h3>
    </div>
</div>
<div class="container">
    <div class="row" id="page_search_none" hidden>
        <div class="col s12">
            <p class="flow-text center-align">
                We found nothing related to your search.<br>
                Please check the spelling and try again.<br>
            </p>
        </div>
    </div>
    <div class="row">
    {% for game in site.data.games.list %}
        <div class="col s12 m6 l4" id="col-{{forloop.index}}">
            <div class="card-search" hidden>
                <div class="card-id">col-{{forloop.index}}</div>
                <div class="name">{{game.name}}</div>
                <div class="tags">{{game.tags}}</div>
            </div>
            <div class="card">
                {% if game.ext_link != null and game.ext_link != "" %}
                <a href="{{game.ext_link}}">
                {% endif %}
                <div class="card-content">
                    <span class="card-title grey-text text-darken-4">
                        {{game.name}}
                        {% if game.ext_link != null and game.ext_link != "" %}
                        <i class="material-icons tiny valign-top">launch</i>
                        {% endif %}
                        <small>
                            <span class="right">{{game.min_players}}-{{game.max_players}}</span>
                            <i class="material-icons right">person</i>
                        </small>
                    </span>
                </div>
                <div class="card-content no-space-top">
                  <p>
                    {{game.description}}<br><br>
                    {% assign tags = game.tags | split: "," %}
                    {% for tag in tags %}
                    <span class="badge">#{{tag}}</span>
                    {% endfor %}
                    <br>
                  </p>
                </div>
                {% if game.ext_link != null and game.ext_link != "" %}
                </a>
                {% endif %}
            </div>
        </div>
    {% endfor %}
    </div>
    <br><br>
</div>

<script type="text/javascript" src="/assets/js/similarity-search.js"></script>

<script>
    // range
    var array_of_dom_elements = document.querySelectorAll("input[type=range]");
    M.Range.init(array_of_dom_elements);

    // scrollspy
    document.addEventListener('DOMContentLoaded', function() {
    var elems = document.querySelectorAll('.scrollspy');
    var options = {};
    var instances = M.ScrollSpy.init(elems, options);
    });

    var card_ids = $(".card-id").map(function() {return this.innerHTML;}).get();
    var names = $(".name").map(function() {return this.innerHTML;}).get();
    var tags = $(".tags").map(function() {return this.innerHTML;}).get();

    $( "#search_form" ).submit(function( event ) {
        var similarity_threshold = FORGIVING;
        var str = $("#search_event").val()
        event.preventDefault();

        var names_similarity = [];
        var tags_similarity = [];

        if(str.charAt(0) == "#"){
            for ( var i = 0, l = card_ids.length; i < l; i++ ) {
                $("#" + card_ids[i]).hide();

                var tag = tags[i].split(",")
                var tag_similarity = 0

                for ( var j = 0; j < tag.length; j++ ) {
                    var s = similarity(str, tag[j]) + keyword_reward(str, tag[j]);
                    if ( s > tag_similarity ) {
                        tag_similarity = s
                    }
                }

                tags_similarity.push(tag_similarity);
            }
        }
        else if(str != ""){
            for ( var i = 0, l = card_ids.length; i < l; i++ ) {
                $("#" + card_ids[i]).hide();
                var name_similarity = similarity(str, names[i]) + keyword_reward(str, names[i]);

                names_similarity.push(name_similarity);
            }
        }
        else if(str == ""){ // return all cards
            for ( var i = 0, l = card_ids.length; i < l; i++ ) {
                $("#" + card_ids[i]).hide();
                var name_similarity = FORGIVING + 1;

                names_similarity.push(name_similarity);
            }
        }

        var cards_shown = 0;

        for ( var i = 0, l = card_ids.length; i < l; i++) {
            if(parseFloat(similarity_threshold) < parseFloat(names_similarity[i]) || parseFloat(similarity_threshold) < parseFloat(tags_similarity[i]))
            {
                $("#" + card_ids[i]).show();
                cards_shown++;
            }
        }

        if(cards_shown < 1)
        {
            $("#page_search_none").show();
        }
        else
        {
            $("#page_search_none").hide();
        }

        $("#search_event").val('');
        $("#search_event").blur();
    });
</script>
