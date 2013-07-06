---
layout: post
title: "Active Record"
date: 2013-07-05 11:30
comments: true
categories: [WDI, Active Record, API, Gem]
---
## Week 3 Day 5 ##
Over the past two days we were assigned a homework project involving the Sunlight API Gem. [Sunlight](https://github.com/sunlightlabs/ruby-sunlight "Sunlight") provides various methods for obtaining information on members of congress, legislator IDs, and 'look-ups' between places and the politicians that represent them. The project involved the latter search method. By providing the user with a search form, a zipcode is entered and then routed to (using Sinatra). Here is an example of the search method we used to GET the politicians from Sunlight and present them on another page:
{% codeblock lang:ruby %}
get '/:zipcode' do
  @zipcode = params[:zipcode]
  #Defining instance variable with Sunlight app
  @politicians = Sunlight::Legislator.all_in_zipcode(@zipcode)
  erb :politicians_in_zipcode
end
{% endcodeblock %}
From that page (erb :politicians_in_zipcode) we were encouraged to add the ability to favorite or like certain politicians and then list them back on the index page where the search form exists. In addition to a like button, each poltician's party is identified by their parties color.

Democrat = Blue | Republican = Red | Independent = Purple
{% codeblock lang:html %}
  <%color = '#9A2EFE'%>
      <% if party == "D" %>
        <% color = '#2E64FE' %>
      <% elsif party == "R" %>
        <% color = '#FE2E2E' %>

    <% end %>

    <h3 style = "color: <%=color%>">
    <%=person.firstname%> <%=person.lastname%>
{% endcodeblock %}

{% img right /images/Brooklyn.png 400 350 'Brooklyn_ZipCode' %}
<p>An example from my home zipcode yields only members of the Democratic party. So, in order to display the working color code I used 11209, an area code assigned to Brooklyn, NY.
The highest difficulty I experienced in building this app was adding selected politicians to a "liked" list (essentially passing them into the ActiveRecord Database) while retaining the information provided by Sunlight. For accessiblity purposes, the form entry for liking a politician requires the press of a button. Here is the form I made:
</p>

<p>
  {% codeblock lang:html %}
  <form action = '/like' method = 'post'>
    <input type = "hidden" name = "votesmart_id" value="<%= person.votesmart_id%>">
      <button> Like </button>
  </form>
{% endcodeblock %}
</p>
What occurs when the user presses the Like button, is a transfer of the value :person.votesmart_id (unique ID assigned to politicians) to the POST method in main.rb-
  {% codeblock lang:ruby %}
  post '/like' do
    liked = Sunlight::Legislator.all_where(:votesmart_id => params[:votesmart_id]).first

    Politicians.create(
                    :firstname => liked.firstname,
                    :lastname => liked.lastname,
                    :party => liked.party,
                    :state => liked.state,
                    :twitter_id => liked.twitter_id,
                    :in_office => liked.in_office,
                    :votesmart_id => liked.votesmart_id
                    )
  redirect to('/')
  end
  {% endcodeblock %}


This calls for a minor adjustment to the index page in order to display all the 'liked' politicians. By writing <code>@politicians = Politicians.all</code> the liked politicians will be listed below the search form.
The code displayed above was relatively challenging for me. I was constantly running into a Postgres error (database error) which could not find a <code>politicians</code> class. Although I am still unsure why it occured, it was explained to me that the error resided in my migration file. When creating a new entry into my ":politician" table 'create_table :politician do |t|', ActiveRecord was looking for a :politician*s* table. Meaning it should have been plural all along. I hope to learn why the table must be plural, but either way it's obviously crucial to be hyper-aware of the syntax when creating a migration file. As of right now, the app is up and running.

Index Screenshot:

{% img inline /images/Web.png auto auto 'Web Browser' %}


GitHub link- [Here](https://github.com/Swelly/sunlight "Sunlight - Swelly")
