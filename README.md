1.  Create a new Rails application called "BookManager", switch directories (`cd`) into this Rails app from the command line, create a git repository and save the initial files with the commit message "Initial commit".

2.  Create three models and the scaffolding from the command line using the command:

        rails generate scaffold <Model> <attribute>:<data type>

    Below are the details of each model:

    ### Book

    *   title (string)
    *   publisher_id (integer)
    *   proposal_date (date)
    *   contract_date (date)
    *   published_date (date)
    *   units_sold (integer)

    ### Publisher

    *   name (string)

    ### Author

    *   first_name (string)
    *   last_name (string)
3.  Create an additional model called `BookAuthor` using the `rails generate model` command. The attributes of this model are `book_id (integer)` and `author_id (integer)`.

    We don't need a full set of views or a controller, just an associative entity to connect books and authors, so using a model generator is sufficient in this case.

    After creating these models, migrate the database and save all this generated code to git.

4.  Create and switch to a new branch in git called "models". Open the `Book` model in your editor and add the following three relationships to that model:

    <div class="highlight highlight-ruby">

    <pre><span class="n">belongs_to</span> <span class="ss">:publisher</span>                     
    <span class="n">has_many</span> <span class="ss">:book_authors</span>
    <span class="n">has_many</span> <span class="ss">:authors</span><span class="p">,</span> <span class="ss">through</span><span class="p">:</span> <span class="ss">:book_authors</span>              
    </pre>

    </div>

    In addition, add the following validations:

    <div class="highlight highlight-ruby">

    <pre><span class="n">validates_presence_of</span> <span class="ss">:title</span>                        
    <span class="n">validates_numericality_of</span> <span class="ss">:year_published</span> 
    </pre>

    </div>

    For the `units_sold`, refer to the Rails API at [apidock.com](http://apidock.com/rails) for the option that restricts the values to integers only.

    Finally, add the following two scopes:

    <div class="highlight highlight-ruby">

    <pre><span class="n">scope</span> <span class="ss">:alphabetical</span><span class="p">,</span> <span class="o">-></span> <span class="p">{</span> <span class="n">order</span><span class="p">(</span><span class="s1">'title'</span><span class="p">)</span> <span class="p">}</span>
    </pre>

    </div>

5.  Go to the Publisher model and add the following relationship:

    <div class="highlight highlight-ruby">

    <pre><span class="n">has_many</span> <span class="ss">:books</span> 
    </pre>

    </div>

    In addition, add the following validations and named scopes:

    <div class="highlight highlight-ruby">

    <pre><span class="n">validates_presence_of</span> <span class="ss">:name</span>
    <span class="n">scope</span> <span class="ss">:alphabetical</span><span class="p">,</span> <span class="o">-></span> <span class="p">{</span> <span class="n">order</span><span class="p">(</span><span class="s1">'name'</span><span class="p">)</span> <span class="p">}</span>
    </pre>

    </div>

6.  Go to the Author model and add the following relationship:

    <div class="highlight highlight-ruby">

    <pre><span class="n">has_many</span> <span class="ss">:book_authors</span>
    <span class="n">has_many</span> <span class="ss">:books</span><span class="p">,</span> <span class="ss">through</span><span class="p">:</span> <span class="ss">:book_authors</span>                    
    </pre>

    </div>

    In addition, add the following validations, scopes and methods:

    <div class="highlight highlight-ruby">

    <pre><span class="n">validates_presence_of</span> <span class="ss">:first_name</span><span class="p">,</span> <span class="ss">:last_name</span> 
    <span class="n">scope</span> <span class="ss">:alphabetical</span><span class="p">,</span> <span class="o">-></span> <span class="p">{</span> <span class="n">order</span><span class="p">(</span><span class="s1">'last_name, first_name'</span><span class="p">)</span> <span class="p">}</span>

    <span class="k">def</span> <span class="nf">name</span>
      <span class="s2">"</span><span class="si">#{</span><span class="n">last_name</span><span class="si">}</span><span class="s2">,</span> <span class="si">#{</span><span class="n">first_name</span><span class="si">}</span><span class="s2">"</span>
    <span class="k">end</span>
    </pre>

    </div>

7.  Go to the BookAuthor model and add the following relationship:

    <div class="highlight highlight-ruby">

    <pre><span class="n">belongs_to</span> <span class="ss">:book</span>
    <span class="n">belongs_to</span> <span class="ss">:author</span>
    </pre>

    </div>

    Once the model code is complete, restart the server and be sure it is all running and that there are no typos (common error) in your code by looking at the index pages of books, authors and publishers. 

    ### Indexes

    * http://localhost:3000/books
    * http://localhost:3000/publishers
    * http://localhost:3000/authors

    If it’s all good, commit these changes to your git repo and then merge these changes into the master branch. Reminder, that should look like `git checkout master` to get back to the master branch and `git merge models` to incorporate your most recent updates.

8) 
    *   Before we dive into views, we are going to complete some of the missing validations in the Book model. Create and move to a new book branch with `git checkout -b book`.

    *  Since Rails does not have built-in date validations, we are using the [validates_timeliness gem](https://github.com/adzap/validates_timeliness); find the documentation for this gem by clicking on the link and scroll down the webpage to see some example uses. 

    * First, we need to include the gem in our project. We do this by going to the Gemfile and adding in `'gem 'validates_timeliness'`. Now run `bundle update` to make sure you have the gem on your machine. Lastly, it is necessary to also run the `rails generate validates_timeliness:install` command to have a couple of files generated for the project, so do so now to make sure that you have them.

    *   Add a validation so that the `proposal_date` is a legitimate date and that it is either the current date or some time in the past. (The reason is you shouldn't be allowed to record a proposal you haven't yet received.)

    *   Add a validation to `contract_date` to ensure that it is a legitimate date and that it is either the current date or some time in the past. (The reason is you shouldn't be allowed to record a contract you haven't yet signed.)

    Also make sure that the `contract_date` is some time after the proposal_date as you can't sign contracts for books yet to be proposed.

    Finally allow the `contract_date` to be blank as not all books we are tracking have contracts yet.

    *   Add a validation to `published_date` so that it is also a legitimate date and that it is either the current date or some time in the past.

    Also make sure that the `published_date` is some time after the contract_date as you can't publish books without contracts.

    Finally allow the `published_date` to be blank as not all books we are tracking are published yet.

    *   Start (or restart) your rails server and verify that these validations work in the interface. Confirm this with a TA before continuing. Do not merge back into `master`. **2017 Update for Vagrant users**: `rails server` breaks on old labs. Until a better solution is found, open your `Gemfile` and remove `gem 'thin'`. Then run `rails server -b localhost`.

* * *

# <span class="mega-icon mega-icon-issue-opened"></span>Stop

Show a TA that you have the basic Rails app set up and working, and that you have properly saved the code to git. Make sure the TA initials your sheet.

* * *

1.  Now go to the web interface and add a new publisher: "Pragmatic Bookshelf". After that, go to the books section and add a new book: "Agile Web Development with Rails" which was published by Pragmatic Bookshelf in 2011\. Make sure to update the three date fields with appropriate dates. Note that you need to refer to the publisher by its id (1), rather than its name in the current interface. Thinking about this, and some other problems with the current interface, we will begin to make the interface more usable, working now in a new branch called 'interface'

2.  We'll begin by adding some more publishers directly into the database using the command line. If we think back to the SimpleQuotes lab last week, the easiest way to insert new data is by opening a new command line tab in the same directory and running `rails db`. Then paste the publishers_sql and authors_sql code given so that we have multiple publishers and authors to choose from (and sharpen our db skills slightly). **Note**: do not add the first publisher since we have already added Pragmatic Bookshelf via the web interface; if you do you will get an error because they are already in the db with a id=1.

    <div class="highlight highlight-sql">

    <pre><span class="c1">-- SQL for authors</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"authors"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="s1">'Sam'</span><span class="p">,</span> <span class="s1">'Ruby'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"authors"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">2</span><span class="p">,</span> <span class="s1">'Dave'</span><span class="p">,</span> <span class="s1">'Thomas'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"authors"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">3</span><span class="p">,</span> <span class="s1">'Hal'</span><span class="p">,</span> <span class="s1">'Fulton'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"authors"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">4</span><span class="p">,</span> <span class="s1">'Robert'</span><span class="p">,</span> <span class="s1">'Hoekman'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"authors"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">5</span><span class="p">,</span> <span class="s1">'David'</span><span class="p">,</span> <span class="s1">'Hannson'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"authors"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">6</span><span class="p">,</span> <span class="s1">'Dante'</span><span class="p">,</span> <span class="s1">'Alighieri'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"authors"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">7</span><span class="p">,</span> <span class="s1">'William'</span><span class="p">,</span> <span class="s1">'Shakespeare'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"authors"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">8</span><span class="p">,</span> <span class="s1">'Jane'</span><span class="p">,</span> <span class="s1">'Austen'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>

    <span class="c1">-- SQL for publishers</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"publishers"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="s1">'Pragmatic Bookshelf'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"publishers"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">2</span><span class="p">,</span> <span class="s1">'Washington Square Press'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"publishers"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">3</span><span class="p">,</span> <span class="s1">'Addison Wesley'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"publishers"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">4</span><span class="p">,</span> <span class="s1">'Everyman Library'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>
    <span class="k">INSERT</span> <span class="k">INTO</span> <span class="ss">"publishers"</span> <span class="k">VALUES</span> <span class="p">(</span><span class="mi">5</span><span class="p">,</span> <span class="s1">'New Riders'</span><span class="p">,</span> <span class="s1">'2015-02-09 12:00:00'</span><span class="p">,</span> <span class="s1">'2014-02-10 12:00:00'</span><span class="p">);</span>
    </pre>

    </div>

3.  The first thing we will do is switch the 'publisher_id' field (a text box where you are supposed to remember and type out the appropriate publisher's id) to a drop-down list. Now that we have some publishers in the system, go to the `_form` partial in the Books view and change the publisher_id text_field to the following line:

    <div class="highlight highlight-erb">

    <pre><span class="cp"><%=</span> <span class="n">f</span><span class="o">.</span><span class="n">collection_select</span> <span class="ss">:publisher_id</span><span class="p">,</span> <span class="no">Publisher</span><span class="o">.</span><span class="n">alphabetical</span><span class="p">,</span> <span class="ss">:id</span><span class="p">,</span> <span class="ss">:name</span> <span class="cp">%></span><span class="x"></span>
    </pre>

    </div>

    Look at the new form on the web page. It's an improvement (I also like to convert the number_field for year_published to a straight text field, but not required), but it'd be a little nicer if it didn't default to the first publisher. Go to [apidock.com/rails](http://apidock.com/rails) and look up `collection_select` and see if there is an option that will prompt the user for input rather than just display the first record. Implement similar functionality for contract_date and published_date (which are not required). After fixing this, I'd recommend you save this work to your git repository.

4.  Of course, we also need to be able to select one or more authors for each book. In the `_form.html.erb` template for books, add in a partial that will create the checkboxes for assigning an author to a book. Add the line

    <div class="highlight highlight-erb">

    <pre><span class="cp"><%=</span> <span class="n">render</span> <span class="ss">partial</span><span class="p">:</span> <span class="s1">'authors_checkboxes'</span> <span class="cp">%></span><span class="x"></span>
    </pre>

    </div>

    just prior to the submit button in the template.

    Within the `app/views/books` directory, create a new file called `_authors_checkboxes.html.erb` and add to it the following code:

    <div class="highlight highlight-erb">

    <pre><span class="x"><p></span>
     <span class="x"></span> <span class="cp"><%</span> <span class="k">for</span> <span class="n">author</span> <span class="k">in</span> <span class="no">Author</span><span class="o">.</span><span class="n">alphabetical</span> <span class="cp">%></span><span class="x"></span>
     <span class="x"></span> <span class="cp"><%=</span> <span class="n">check_box_tag</span> <span class="s2">"book[author_ids][]"</span><span class="p">,</span> <span class="n">author</span><span class="o">.</span><span class="n">id</span><span class="p">,</span> <span class="vi">@book</span><span class="o">.</span><span class="n">authors</span><span class="o">.</span><span class="n">include?</span><span class="p">(</span><span class="n">author</span><span class="p">)</span> <span class="cp">%></span><span class="x"></span>
     <span class="x"></span> <span class="cp"><%=</span> <span class="n">author</span><span class="o">.</span><span class="n">name</span> <span class="cp">%></span><span class="x"></span>
     <span class="x"><br /></span>
     <span class="x"></span> <span class="cp"><%</span> <span class="k">end</span>  <span class="cp">%></span><span class="x"></span>
    <span class="x"></p></span>
    </pre>

    </div>

    Note: in Rails 3 and above, `render` assumes by default you are rendering a partial, so you could just say `render 'authors_checkboxes'` here, but I want you to put the `:partial =>` in for now to reinforce the idea of partials.

    If you were to try and submit the data for this form, it would reject the information for the authors (You could check this by looking in the BookAuthor table). We will talk about this later in the course. For now, add `:author_ids` to the list of attributes that your controller will allow to be passed to your Book model. We can find that list in a private method called `book_params` at the bottom of the `BooksController` -- add `:author_ids` there. Because this is an array of ids, we need to let Rails know that with the code below:

    <div class="highlight highlight-ruby">

    <pre><span class="c1"># controllers/books_controller.rb</span>
    <span class="k">def</span> <span class="nf">book_params</span>
      <span class="n">params</span><span class="o">.</span><span class="n">require</span><span class="p">(</span><span class="ss">:book</span><span class="p">)</span><span class="o">.</span><span class="n">permit</span><span class="p">(</span><span class="ss">:title</span><span class="p">,</span> <span class="ss">:year_published</span><span class="p">,</span> <span class="ss">:publisher_id</span><span class="p">,</span> <span class="ss">:author_ids</span> <span class="o">=></span> <span class="o">[]</span><span class="p">)</span>
    <span class="k">end</span>
    </pre>

    </div>

5.  In the show template for books, change the `@book.publisher_id` to `@book.publisher.name` so that we are displaying more useful information regarding the publisher. After that, add in a partial that will create a bulleted list of authors for a particular book. To do that, add the line:

    <div class="highlight highlight-erb">

    <pre><span class="cp"><%=</span> <span class="n">render</span> <span class="ss">partial</span><span class="p">:</span> <span class="s1">'list_of_authors'</span> <span class="cp">%></span><span class="x"></span>
    </pre>

    </div>

    after the publisher information in the template and in the code. Then add a file called `_list_of_authors.html.erb` to the app/views/books directory. Within this new file add the following code:

    <div class="highlight highlight-erb">

    <pre><span class="x"><p></span>
     <span class="x"><b></span><span class="cp"><%=</span> <span class="n">pluralize</span><span class="p">(</span><span class="vi">@book</span><span class="o">.</span><span class="n">authors</span><span class="o">.</span><span class="n">size</span><span class="p">,</span> <span class="s1">'Author'</span><span class="p">)</span> <span class="cp">%></span><span class="x"></b><br /></span>
     <span class="x"><ul></span>
     <span class="x"></span> <span class="cp"><%</span> <span class="k">for</span> <span class="n">author</span> <span class="k">in</span> <span class="vi">@book</span><span class="o">.</span><span class="n">authors</span> <span class="cp">%></span><span class="x"></span>
     <span class="x"><li></span><span class="cp"><%=</span> <span class="n">link_to</span><span class="p">((</span><span class="n">author</span><span class="o">.</span><span class="n">name</span><span class="p">),</span> <span class="n">author_path</span><span class="p">(</span><span class="n">author</span><span class="p">))</span> <span class="cp">%></span><span class="x"></li></span>
     <span class="x"></span> <span class="cp"><%</span> <span class="k">end</span>  <span class="cp">%></span><span class="x"></span>
     <span class="x"></ul></span>
    <span class="x"></p></span>
    </pre>

    </div>

6.  After that, add some books to the system using the web interface. Given the authors added, there are some suggested books listed at the end of this lab, but you can do as you wish. View and edit the books to be sure that everything is worked as intended. Of course, looking at the books index page, we realize it too has issues; fix it so that the publisher's name is listed rather than the id (see previous step if you forgot how) and the books are in alphabetical order by using the alphabetical scope in the appropriate place in the books_controller. (Try it yourself, but see a TA if you are struggling on this last one for more than 5 minutes.)

    **BTW, have you been using git after each step? If not, time to do so...**

7.  Look at the partial `list_of_authors`. There are three things to take note of:

    1.  how the pluralize function adds an 's' at the end of author when there is more than one
    2.  how Ruby loops through the list of authors (note that the erb tags for the 'for' and 'end' tag do not have an equal sign)
    3.  how Rails' link_to tag is used to wrap the author's name in an anchor tag leading back to the author's details page.
8.  Before having the TA sign off, you decide to test the following: go to a book in the system, uncheck all the authors, and save. It saves 'successfully', but the list of authors remains unchanged. **Ouch**. Good thing we are testing this app pretty carefully. How do we fix this? First, we need to realize that this happens because if no values are checked for author_ids, then rails by default just doesn't submit the parameter `book[:author_ids][]`. We can force it to submit an empty array by default by adding to the book form (right after the form_for tag) the following line:

    <div class="highlight highlight-erb">

    <pre><span class="cp"><%=</span> <span class="n">hidden_field_tag</span> <span class="s2">"book[author_ids][]"</span><span class="p">,</span> <span class="kp">nil</span><span class="cp">%></span><span class="x"></span>
    </pre>

    </div>

    Once this is working (test it again to be sure), then you can merge the `interface` branch in git back into the `master` branch.

9.  (Optional, but recommended if you have time left in lab) Having developed the interface for books, go back to the`interface` branch and write your own partial for show template of the authors view so that it added a list of all the books the author has written. (This is very similar to what was done for the show book functionality and those instructions/code can guide you.) Once you know it is working properly, save the code to the repo and merge back into the master branch.

# <span class="mega-icon mega-icon-issue-opened"></span>Stop

Show a TA that you have completed the lab. Make sure the TA initials your sheet.

## On Your Own

This week the "on your own" assignment is to go to [RubyMonk's free Ruby Primer](http://rubymonk.com/learning/books/1) and complete any of the previously assigned exercises you have not yet done. If you are caught up and understand the previous exercises (repeat any you are unsure of), you may if time allows choose any of the other primer exercises and try to get ahead (we will do more of these exercises after the exam). Note: be aware that questions from RubyMonk can and will show up on the exam, so 'doing it on your own' does not mean 'doing it if you want to'.

* * *

## Suggested Books

**Agile Web Development with Rails**

*   Year published: 2011
*   Publisher: Pragmatic Bookshelf
*   Authors:
    *   Sam Ruby
    *   David Hannson
    *   Dave Thomas

**Romeo and Juliet**

*   Year published: 2004
*   Publisher: Washington Square Press
*   Authors:
    *   William Shakespeare

**King Lear**

*   Year published: 2004
*   Publisher: Washington Square Press
*   Authors:
    *   William Shakespeare

**The Divine Comedy**

*   Year published: 1995
*   Publisher: Everyman's Library
*   Authors:
    *   Dante Alighieri

**Pride and Prejudice**

*   Year published: 2001
*   Publisher: Washington Square Press
*   Authors:
    *   Jane Austen
