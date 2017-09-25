## Lab 7: Views and Controllers

<div id="wrapper">

<section id="content">

<div class="titlebar"><span class="mini-icon mini-icon-readme"></span>README.md</div>

<div class="markdown-body padded">

1.  For this project we are using another version of the BookManager application we've developed previously. To get a copy of this new project, use git to clone the [BookManager_3 project on github](https://github.com/profh/BookManager_3_starter) and review the contents before proceeding.

2.  If you are on a Mac, you will need to have Node.js and Node Package Manager installed `npm` on your machine. (If you are using the VM, this has already been installed.) You can get it directly with an easy package installer at the [Node Project download page](http://nodejs.org/download/) or if you have homebrew installed, you can run `brew install node` (which should also include npm). If you are on Ubuntu, running `sudo apt-get install nodejs npm` should do the trick.

3.  Run the bundle command to install the new gems such as [simple_form](https://github.com/plataformatec/simple_form) and [foundation-rails](https://github.com/zurb/foundation-rails) used in this project:

    ```
      bundle install

    ```

    Commit these changes to git.

4.  Run `git branch` to see that you are on the master branch. Run the `rake db:migrate` command to generate the development database and then run `rake db:populate` to add records to your development database. Start the server and verify that the basic app is running by looking at the various book lists. (There are no controllers yet for authors and categories, so limit yourself to books for now.)

5.  First thing to do is build some controllers for authors. Open the `authors_controller.rb` file inside of `app/controllers` and within the class definition, let's add some code to restrict the types of parameters we will accept from users. We will make this a private method so that it can only be called by other methods within the class. The code below will help us do that:

    ```ruby
      private

      def author_params
        params.require(:author).permit(:first_name, :last_name, :active)
      end

    ```

6.  When we work with show, edit, update and destroy actions, we will be working with a particular author based on an author id provided by the user via our interface. We want to create this author and assign it to an `@author` object before starting each of these methods. Put the following code at the top of (but still inside) the class definition:

    ```ruby
      before_action :set_author, only: [:show, :edit, :update, :destroy]

    ```

    This 'before filter' will run the set_author method prior to each of these actions. Now go create this private method below the `author_params` method we created earlier with this code:

    ```ruby
      def set_author
        @author = Author.find(params[:id])
      end

    ```

7.  Now time to create our standard RESTful actions: index, show, new, edit, create, update and destroy. We begin with `index`. Create an index method (if you use Sublime or Textmate, type def and hit `tab` and a basic method structure is created) and add the following code to instruct model to give us all active authors in alphabetical order and to use will_paginate to prepare it for pagination:

    ```ruby
      @authors = Author.active.alphabetical.paginate(:page => params[:page]).per_page(10)

    ```

    **Put the index action and all the other standard controller actions above the private methods we created earlier. Everything after the key word 'private' will be a private method and we want actions like index, show, and the like to be public.**

8.  To create actions for show and edit, we just need the basic `def ... end` structure in place for each method. The controller will build the `@author` object for us using the before_filter we set up and it will expect a show and edit template to be in place in the `app/views/authors` directory (which is there but we will improve upon later). For the new method, we need a blank [@author](http://your-domain.com/author) object for the user to populate with data and we can do this easily enough with:

    ```ruby
      def new
        @author = Author.new
      end

    ```

9.  In our create and update actions, we will use our `author_params` method created earlier to make sure the parameters being sent are allowable. If we can create (or update) the [@author](http://your-domain.com/author) object, then we will go to the author's show page and flash a quick message saying the changes where made; otherwise we will go back to the original form and show error messages. The code to do this is below:

    ```ruby
      def create
        @author = Author.new(author_params)
        if @author.save
          redirect_to author_path(@author), notice: "#{@author.name} was added to the system."
        else
          render action: 'new'
        end
      end

      def update
        if @author.update(author_params)
          redirect_to author_path(@author), notice: "#{@author.name} was revised in the system."
        else
          render action: 'edit'
        end
      end

    ```

10.  To create the destroy action we want to destroy the `@author` object and then return to the overall authors list (index). The following code will do that:

    ```ruby
      def destroy
        @author.destroy
        redirect_to authors_url
      end

    ```

11.  Our authors controller is good, but without routes to direct to these actions, they are pretty useless. We could generate the seven routes manually or take advantage of Rails built-in shortcut that was demonstrated in class. Go to the `config/routes.rb` file and add
    `resources :authors` right below similar code for books -- this will generate the seven standard routes we need to use this controller properly.

12.  Verify that the routes and controller are working in the browser and then commit your changes to git if you have not done so already. (Hopefully you have...) If time allows, follow a similar procedure to build your categories controller. You will need to add a resources line to your routes file for categories, and create the same set of methods in the categories controller.

* * *

# <span class="mega-icon mega-icon-issue-opened"></span>Stop

Show a TA that you have completed the first part. Make sure the TA initials your sheet.

* * *

1.  As you can see, with essentially no CSS, the app functions appropriately, but does not look particularly attractive and seems unprofessional. We are going to use two gems that we installed earlier - [simple_form](https://github.com/plataformatec/simple_form) and [foundation-rails](https://github.com/zurb/foundation-rails) - to help us fix this up. If you look at the documentation, you see that both require us to run a generator to add some code to the project. Do that first for Foundation with the command:

    ```
      rails generate foundation:install

    ```

    There will be a conflict with the existing application stylesheet because it is adding in an overrides file that we want, so press 'Y' to overwrite it. Go into the application.css file and comment out the existing CSS code (lines 17-128) so that we are just using Foundation's CSS.

    Once that is done we want to run the simple_form generator. If you don't read the documentation carefully, you will skip adding the Foundation options that we definitely want. To get those options we use the command:

    ```
      rails generate simple_form:install --foundation

    ```

    This will install some config files so we will **need to restart the server** before being able to use the gem.

2.  We want to use Foundation's grid system that we worked with in [lab 2](http://67272.cmuis.net/labs/2) to organize the application layout file to have three sections: a main content section, a small spacer, and a side bar with some quick links for navigation ease. Replace the body tag in the application layout with the following:

    ```erb
      <body>
        <div class="row">
          <div class="small-8 columns">
            <!-- MAIN CONTENT GOES HERE -->
            <%= yield %>
            <%= javascript_include_tag "application" %>
          </div>

          <div class="small-1 columns">
            &nbsp;
          </div>

          <div class="small-3 columns">
            <!-- SIDEBAR CONTENT GOES HERE -->
            <%= render partial: 'partials/quick_links' %>
          </div>
        </div>

        <footer>
          <div class="row">
            <div class="small-6 large-centered columns">
              <%= render partial: 'partials/footer' %>
            </div>
          </div> 
        </footer>     
      </body>

    ```

    Some partials have been provided already in the starter code, but you can edit them as appropriate. Since we are adding the links on the side, go into the books views and remove the links at the bottom of the index, contracted, and proposed views.

3.  We need to add a navigation bar to top of the page. There is great documentation for this task at [http://foundation.zurb.com/sites/docs/v/5.5.3/components/topbar.html](http://foundation.zurb.com/sites/docs/v/5.5.3/components/topbar.html) if you want to customize further. For now, just inside the body tag of the application layout, add the following code:

    ```erb
      <div class="contain-to-grid">
        <nav class="top-bar" data-topbar>
          <ul class="title-area">
            <li class="name">
              <h1><%= link_to "BookManager", home_path %></h1>
            </li>
          </ul>
          <section class="top-bar-section">
            <ul class="left">
              <li><%= link_to "Categories", categories_path %></li> <!-- Only add this line if you did the work for the categories controller -->
              <li class="has-dropdown">
                <a href="#">Authors</a>
                <ul class="dropdown">
                  <li><%= link_to "List Authors", authors_path %></li>
                  <li><%= link_to "Add an Author", new_author_path %></li>
                </ul>
              </li>
            </ul>
          </section>
        </nav>
      </div>

    ```

    View in the browser to see these changes. It should be obvious that something is lacking -- menu links for books. Edit the code given to incorporate books and the different options (all, proposed, and the like). Be sure to commit your files if you haven't already.

4.  We want to turn the edit and destroy links in the `books#index` view into buttons. To do this, add to the edit link: `class="button tiny radius"` and to the destroy link: `class: "button tiny round alert"`. View these changes in the browser. We want to change that alert color from orange to red, so go to `app/assets/stylesheets/` and open the file `foundation_and_overrides.scss` and uncomment line 121 and replace it with `$alert-color: #E6172C;` To change the size of text with those buttons, go to line 456, uncomment it and replace with `$button-font-tny: rem-calc(9);`. Then adjust the padding by going to line 443, uncomment it and replace with `$button-tny: rem-calc(6);`. Reload the page and view the changes to see that is what you want. Feel free to adjust other options with Foundation as you wish.

**If your `foundation_and_overrides.scss` does not have enough lines, then you may be using a newer version of Foundation called Foundation 6.** If this is the case, go into the _settings.scss file and edit the variables instead.

1.  We will edit the table in `books#index` a bit by first changing the background stripes to `$table-even-row-bg: #CEE8EB;` on line 1299 of the overrides file. Then get rid of the border by editing line 1302 to `$table-border-style: none;`. Finally collapse the rows to get rid of the small gaps in each by first adding `class="table"` to the table tag in the `index.html.erb file` and then adding the following code to the end of the overrides file (starting at line 1495):

    ```css
      .table {
        border-collapse:collapse;
      }

    ```

    Make other customizations to tables as you wish -- see [http://foundation.zurb.com/sites/docs/v/5.5.3/components/tables.html](http://foundation.zurb.com/sites/docs/v/5.5.3/components/tables.html) for more information on options here. As time allows, make similar changes to the other books views.

2.  To fix the books form, we are going to first gut everything but the first and last lines. Then change the `form_for` method on line 1 to `simple_form_for` and after `@book` (but still in the parens and be sure to add a comma to separate the args), add the following:

    ```erb
      html: { class: 'form-horizontal' }

    ```

3.  Within the form block, we are going first add a set of `<fieldset>` tags and put all our form elements within fieldset. The first tag we will add within `<fieldset>` is a legend; the form should now look like the sample seen below:

    ```erb
      <%= simple_form_for @book, html: { class: 'form-horizontal' } do |f| %>
        <fieldset>
          <legend><%= controller.action_name.capitalize %> Book</legend>

        </fieldset>
      <% end %>

    ```

4.  Now to start adding actual form fields, we add after the legend the following:

    ```erb
      <%= f.input :title %>

    ```

    Simple form will take care of the label and add inline error messages all by itself. Associations are just as easy, as can be seen for category_id:

    ```erb
      <%= f.association :category %>

    ```

    The select menu can be replaced with radio buttons just by adding the option `as: :radio_buttons`. Reload the form in the browser and see it starting to take shape.

5.  Continuing to build the form, add the following fields and reload the page:

    ```erb
      <%= f.input :units_sold %>
      <%= f.input :proposal_date, as: :date %>
      <%= f.input :contract_date, as: :date %>
      <%= f.input :published_date, as: :date %>
      <%= f.input :notes %>

    ```

6.  Looking at the result, the problem with dates is several-fold:
    a) the size of the fields are too big and overwhelm the form
    b) the order is 'geek-speak' and not traditional month-day-year (a pet peeve of mine, as you will learn)
    c) the proposal date defaulting to current date is okay, but others should be blank by default
    d) the years backwards should be one or two and no year ahead of this year (no option to choose 5 years ahead)

7.  To tackle the first issue (a), add `select.date { width: 90px; }` to the overrides.css file we were working with earlier (put this at the end of the file). Reload the page and see the new change to the form.

8.  To deal with the second issue (b), add `order: [:month, :day, :year]` to each date option after the `as: :date` option. (**Be sure to separate these with a comma; otherwise you will get an error.**) Reload the page and see the new change to the form.

9.  To deal with the third issue (c), add `include_blank: true` to contract and published dates after the `:order` option. (**Again, be sure to separate these with a comma; otherwise you will get an error.**) Reload the page and see the new change to the form.

10.  To deal with the fourth issue (d), add the code below to each date. Reload the page and see the new change to the form.

    ```ruby
      start_year: Date.today.year - 2, end_year: Date.today.year

    ```

11.  Now we need to add a list of authors who are connected with the book. Last time we needed a partial and the logic was a little tricky (had to cover the case where all were unchecked, etc.). With simple_form this is easy to do with a collection:

    ```erb
      <%= f.input :author_ids, label: "Author(s)", collection: Author.active.alphabetical, as: :check_boxes %>

    ```

12.  The form is nearly complete, but we still need to add the actual submit button. Do this with the following code:

    ```erb
      <div class="form-actions">
        <%= f.submit nil, class: 'button' %>
        <%= link_to 'Cancel', books_path, :class => 'button secondary' %>
      </div>

    ```

    Reload the page to see these changes in effect. Use the form to create a new book in the system.

13.  One thing you might have noticed after using the form is that the usual flash message confirming our result did not appear. To correct that, go back the the application layout and add the following code after the main content's container div, but before the row div:

    ```erb
      <% flash.each do |name, msg| %>
        <div class="alert-box <%= :notice ? "success" : "warning" %> radius">
          <a href="#" class="close">&times;</a>
          <%= msg %>
        </div>
      <% end %>

    ```

    If time allows, attempt to get the other forms in order using the simple_form gem. The documentation at [simple_form's github site](https://github.com/plataformatec/simple_form) is really excellent and can help you explore other options you might need. Merge all these commits back to master and get the TA to sign your sheet after reviewing the results and your git log.

    There is obviously so much more to Foundation, but this is a good introduction to using it in a dynamic Rails project that could prove helpful as you start working on Phase 3\. Please note that you will have to customize Foundation if you choose to use it in Phase 3 so that it has a distinctive look (expect a small grade penalty for a straight deploy of the Foundation CSS), but this is relatively easy to do as we saw at the beginning.

* * *

# <span class="mega-icon mega-icon-issue-opened"></span>Stop

Show a TA that you have simple_forms working for the book form and that it is formatted properly. Also show the TA your git log so he/she can see that you've made regular commits and have merged the final results back to master. Make sure the TA initials your sheet.

* * *

</div>

</section>

</div>

<footer>

[About CMUIS](/about) :: [Course Policies](/policies) :: [Course Objectives](/objectives) :: [Department Policies](/department) :: [Who Knows?](http://www.quotationspage.com/quotes/C._S._Lewis/)

Webmaster: [Prof. H](mailto:profh@cmu.edu)   Copyright: 2014 [Prof. H](mailto:profh@cmu.edu)

</footer>
