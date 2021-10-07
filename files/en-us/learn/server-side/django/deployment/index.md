---
title: 'Django Tutorial Part 11: Deploying Django to production'
slug: Learn/Server-side/Django/Deployment
tags:
  - Beginner
  - CodingScripting
  - Deployment
  - Django deployment
  - django
  - web server
---
<div>{{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/Server-side/Django/Testing", "Learn/Server-side/Django/web_application_security", "Learn/Server-side/Django")}}</div>

<p>Now you've created (and tested) an awesome <a href="/en-US/docs/Learn/Server-side/Django/Tutorial_local_library_website">LocalLibrary</a> website, you're going to want to install it on a public web server so that it can be accessed by library staff and members over the Internet. This article provides an overview of how you might go about finding a host to deploy your website, and what you need to do in order to get your site ready for production.</p>

<table>
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td>Complete all previous tutorial topics, including <a href="/en-US/docs/Learn/Server-side/Django/Testing">Django Tutorial Part 10: Testing a Django web application</a>.</td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To learn where and how you can deploy a Django app to production.</td>
  </tr>
 </tbody>
</table>

<h2 id="Overview">Overview</h2>

<p>Once your site is finished (or finished "enough" to start public testing) you're going to need to host it somewhere more public and accessible than your personal development computer.</p>

<p>Up to now you've been working in a development environment, using the Django development web server to share your site to the local browser/network, and running your website with (insecure) development settings that expose debug and other private information. Before you can host a website externally you're first going to have to:</p>

<ul>
 <li>Make a few changes to your project settings.</li>
 <li>Choose an environment for hosting the Django app.</li>
 <li>Choose an environment for hosting any static files.</li>
 <li>Set up a production-level infrastructure for serving your website.</li>
</ul>

<p>This tutorial provides some guidance on your options for choosing a hosting site, a brief overview of what you need to do in order to get your Django app ready for production, and a worked example of how to install the LocalLibrary website onto the <a href="https://www.heroku.com/">Heroku</a> cloud hosting service.</p>

<h2 id="What_is_a_production_environment">What is a production environment?</h2>

<p>The production environment is the environment provided by the server computer where you will run your website for external consumption. The environment includes:</p>

<ul>
 <li>Computer hardware on which the website runs.</li>
 <li>Operating system (e.g. Linux, Windows).</li>
 <li>Programming language runtime and framework libraries on top of which your website is written.</li>
 <li>Web server used to serve pages and other content (e.g. Nginx, Apache).</li>
 <li>Application server that passes "dynamic" requests between your Django website and the webserver.</li>
 <li>Databases on which your website is dependent.</li>
</ul>

<div class="notecard note">
  <p><strong>Note:</strong> Depending on how your production is configured you might also have a reverse proxy, load balancer, etc.</p>
</div>

<p>The server computer could be located on your premises and connected to the Internet by a fast link, but it is far more common to use a computer that is hosted "in the cloud". What this actually means is that your code is run on some remote computer (or possibly a "virtual" computer) in your hosting company's data center(s). The remote server will usually offer some guaranteed level of computing resources (e.g. CPU, RAM, storage memory, etc.) and Internet connectivity for a certain price.</p>

<p>This sort of remotely accessible computing/networking hardware is referred to as <em>Infrastructure as a Service (IaaS)</em>. Many IaaS vendors provide options to preinstall a particular operating system, onto which you must install the other components of your production environment. Other vendors allow you to select more fully-featured environments, perhaps including a complete Django and web-server setup.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Pre-built environments can make setting up your website very easy because they reduce the configuration, but the available options may limit you to an unfamiliar server (or other components) and may be based on an older version of the OS. Often it is better to install components yourself, so that you get the ones that you want, and when you need to upgrade parts of the system, you have some idea of where to start!</p>
</div>

<p>Other hosting providers support Django as part of a <em>Platform as a Service</em> (PaaS) offering. In this sort of hosting you don't need to worry about most of your production environment (web server, application server, load balancers) as the host platform takes care of those for you (along with most of what you need to do in order to scale your application). That makes deployment quite easy, because you just need to concentrate on your web application and not all the other server infrastructure.</p>

<p>Some developers will choose the increased flexibility provided by IaaS over PaaS, while others will appreciate the reduced maintenance overhead and easier scaling of PaaS. When you're getting started, setting up your website on a PaaS system is much easier, and so that is what we'll do in this tutorial.</p>

<div class="notecard note">
  <p><strong>Note:</strong> If you choose a Python/Django-friendly hosting provider they should provide instructions on how to set up a Django website using different configurations of webserver, application server, reverse proxy, etc (this won't be relevant if you choose a PaaS). For example, there are many step-by-step guides for various configurations in the <a href="https://www.digitalocean.com/community/tutorials?q=django">Digital Ocean Django community docs</a>.</p>
</div>

<h2 id="Choosing_a_hosting_provider">Choosing a hosting provider</h2>

<p>There are well over 100 hosting providers that are known to either actively support or work well with Django (you can find a fairly exhaustive list at <a href="https://djangofriendly.com/index.html">DjangoFriendly hosts</a>). These vendors provide different types of environments (IaaS, PaaS), and different levels of computing and network resources at different prices.</p>

<p>Some of the things to consider when choosing a host:</p>

<ul>
 <li>How busy your site is likely to be and the cost of data and computing resources required to meet that demand.</li>
 <li>Level of support for scaling horizontally (adding more machines) and vertically (upgrading to more powerful machines) and the costs of doing so.</li>
 <li>Where the supplier has data centres, and hence where access is likely to be fastest.</li>
 <li>The host's historical uptime and downtime performance.</li>
 <li>Tools provided for managing the site — are they easy to use and are they secure (e.g. SFTP vs FTP).</li>
 <li>Inbuilt frameworks for monitoring your server.</li>
 <li>Known limitations. Some hosts will deliberately block certain services (e.g. email). Others offer only a certain number of hours of "live time" in some price tiers, or only offer a small amount of storage.</li>
 <li>Additional benefits. Some providers will offer free domain names and support for SSL certificates that you would otherwise have to pay for.</li>
 <li>Whether the "free" tier you're relying on expires over time, and whether the cost of migrating to a more expensive tier means you would have been better off using some other service in the first place!</li>
</ul>

<p>The good news when you're starting out is that there are quite a few sites that provide "evaluation", "developer", or "hobbyist" computing environments for "free". These are always fairly resource constrained/limited environments, and you do need to be aware that they may expire after some introductory period. They are however great for testing low traffic sites in a real environment, and can provide an easy migration to paying for more resources when your site gets busier. Popular choices in this category include <a href="https://www.heroku.com/">Heroku</a>, <a href="https://www.pythonanywhere.com/">Python Anywhere</a>, <a href="https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-free-tier.html">Amazon Web Services</a>, <a href="https://azure.microsoft.com/en-us/pricing/details/app-service/">Microsoft Azure</a>, etc.</p>

<p>Many providers also have a "basic" tier that provides more useful levels of computing power and fewer limitations. <a href="https://www.digitalocean.com/">Digital Ocean</a> and <a href="https://www.pythonanywhere.com/">Python Anywhere</a> are examples of popular hosting providers that offer a relatively inexpensive basic computing tier (in the $5 to $10USD per month range).</p>

<div class="notecard note">
  <p><strong>Note:</strong> Remember that price is not the only selection criterion. If your website is successful, it may turn out that scalability is the most important consideration.</p>
</div>

<h2 id="Getting_your_website_ready_to_publish">Getting your website ready to publish</h2>

<p>The <a href="/en-US/docs/Learn/Server-side/Django/skeleton_website">Django skeleton website</a> created using the <em>django-admin</em> and <em>manage.py</em> tools are configured to make development easier. Many of the Django project settings (specified in <strong>settings.py</strong>) should be different for production, either for security or performance reasons.</p>

<div class="notecard note">
  <p><strong>Note:</strong> It is common to have a separate <strong>settings.py</strong> file for production, and to import sensitive settings from a separate file or an environment variable. This file should then be protected, even if the rest of the source code is available on a public repository.</p>
</div>

<p>The critical settings that you must check are:</p>

<ul>
 <li><code>DEBUG</code>. This should be set as <code>False</code> in production (<code>DEBUG = False</code>). This stops the sensitive/confidential debug trace and variable information from being displayed.</li>
 <li><code>SECRET_KEY</code>. This is a large random value used for CSRF protection etc. It is important that the key used in production is not in source control or accessible outside the production server. The Django documents suggest that this might best be loaded from an environment variable or read from a server-only file.
  <pre class="brush: python"># Read SECRET_KEY from an environment variable
import os
SECRET_KEY = os.environ['SECRET_KEY']

# OR

# Read secret key from a file
with open('/etc/secret_key.txt') as f:
    SECRET_KEY = f.read().strip()</pre>
 </li>
</ul>

<p>Let's change the <em>LocalLibrary</em> application so that we read our <code>SECRET_KEY</code> and <code>DEBUG</code> variables from environment variables if they are defined, but otherwise use the default values in the configuration file.</p>

<p>Open <strong>/locallibrary/settings.py</strong>, disable the original <code>SECRET_KEY</code> configuration and add the new lines as shown below.
During development no environment variable will be specified for the key, so the default value will be used (it shouldn't matter what key you use here, or if the key "leaks", because you won't use it in production).</p>

<pre class="brush: python"># SECURITY WARNING: keep the secret key used in production secret!
# SECRET_KEY = "cg#p$g+j9tax!#a3cup@1$8obt2_+&amp;k3q+pmu)5%asj6yjpkag"
import os
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY', 'cg#p$g+j9tax!#a3cup@1$8obt2_+&amp;k3q+pmu)5%asj6yjpkag')
</pre>

<p>Then comment out the existing <code>DEBUG</code> setting and add the new line shown below.</p>

<pre class="brush: python"># SECURITY WARNING: don't run with debug turned on in production!
# DEBUG = True
DEBUG = os.environ.get('DJANGO_DEBUG', '') != 'False'
</pre>

<p>The value of the <code>DEBUG</code> will be <code>True</code> by default, but will only be <code>False</code> if the value of the <code>DJANGO_DEBUG</code> environment variable is set to <code>False</code>. Please note that environment variables are strings and not Python types. We therefore need to compare strings. The only way to set the <code>DEBUG</code> variable to <code>False</code> is to actually set it to the string <code>False</code></p>

<p>You can set the environment variable to False by issuing the following command:</p>

<pre class="brush: bash">export DJANGO_DEBUG=False</pre>

<p>A full checklist of settings you might want to change is provided in <a href="https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/">Deployment checklist</a> (Django docs). You can also list a number of these using the terminal command below:</p>

<pre class="brush: python">python3 manage.py check --deploy
</pre>

<h2 id="Example_Installing_LocalLibrary_on_Heroku">Example: Installing LocalLibrary on Heroku</h2>

<p>This section provides a practical demonstration of how to install <em>LocalLibrary</em> on the <a href="https://heroku.com">Heroku PaaS cloud</a>.</p>

<h3 id="Why_Heroku">Why Heroku?</h3>

<p>Heroku is one of the longest running and popular cloud-based PaaS services. It originally supported only Ruby apps, but now can be used to host apps from many programming environments, including Django!</p>

<p>We are choosing to use Heroku for several reasons:</p>

<ul>
 <li>Heroku has a <a href="https://www.heroku.com/pricing">free tier</a> that is <em>really</em> free (albeit with some limitations).</li>
 <li>As a PaaS, Heroku takes care of a lot of the web infrastructure for us. This makes it much easier to get started, because you don't worry about servers, load balancers, reverse proxies, or any of the other web infrastructure that Heroku provides for us under the hood.</li>
 <li>While it does have some limitations these will not affect this particular application. For example:
  <ul>
   <li>Heroku provides only short-lived storage so user-uploaded files cannot safely be stored on Heroku itself.</li>
   <li>The free tier will sleep an inactive web app if there are no requests within a half hour period. The site may then take several seconds to respond when it is woken up.</li>
   <li>The free tier limits the time that your site is running to a certain amount of hours every month (not including the time that the site is "asleep"). This is fine for a low use/demonstration site, but will not be suitable if 100% uptime is required.</li>
   <li>Other limitations are listed in <a href="https://devcenter.heroku.com/articles/limits">Limits</a> (Heroku docs).</li>
  </ul>
 </li>
 <li>Mostly it just works, and if you end up loving it, scaling your app is very easy.</li>
</ul>

<p>While Heroku is perfect for hosting this demonstration it may not be perfect for your real website. Heroku makes things easy to set up and scale, at the cost of being less flexible, and potentially a lot more expensive once you get out of the free tier.</p>

<h3 id="How_does_Heroku_work">How does Heroku work?</h3>

<p>Heroku runs Django websites within one or more "<a href="https://devcenter.heroku.com/articles/dynos">Dynos</a>", which are isolated, virtualized Unix containers that provide the environment required to run an application. The dynos are completely isolated and have an <em>ephemeral</em> file system (a short-lived file system that is cleaned/emptied every time the dyno restarts). The only thing that dynos share by default are application <a href="https://devcenter.heroku.com/articles/config-vars">configuration variables</a>. Heroku internally uses a load balancer to distribute web traffic to all "web" dynos. Since nothing is shared between them, Heroku can scale an app horizontally by adding more dynos (though of course you may also need to scale your database to accept additional connections).</p>

<p>Because the file system is ephemeral you can't install services required by your application directly (e.g. databases, queues, caching systems, storage, email services, etc). Instead Heroku web applications use backing services provided as independent "add-ons" by Heroku or 3rd parties. Once attached to your web application, the dynos access the services using information contained in application configuration variables.</p>

<p>In order to execute your application Heroku needs to be able to set up the appropriate environment and dependencies, and also understand how it is launched. For Django apps we provide this information in a number of text files:</p>

<ul>
 <li><strong>runtime.txt</strong>: the programming language and version to use.</li>
 <li><strong>requirements.txt</strong>: the Python component dependencies, including Django.</li>
 <li><strong>Procfile</strong>: A list of processes to be executed to start the web application. For Django this will usually be the Gunicorn web application server (with a <code>.wsgi</code> script).</li>
 <li><strong>wsgi.py</strong>: <a href="https://wsgi.readthedocs.io/en/latest/what.html">WSGI</a> configuration to call our Django application in the Heroku environment.</li>
</ul>

<p>Developers interact with Heroku using a special client app/terminal, which is much like a Unix Bash shell. This allows you to upload code that is stored in a git repository, inspect the running processes, see logs, set configuration variables and much more!</p>

<p>In order to get our application to work on Heroku we'll need to put our Django web application into a git repository, add the files above, integrate with a database add-on, and make changes to properly handle static files.</p>

<p>Once we've done all that we can set up a Heroku account, get the Heroku client, and use it to install our website.</p>

<div class="notecard note">
  <p><strong>Note:</strong> The instructions below reflect how to work with Heroku at time of writing. If Heroku significantly change their processes, you may wish to instead check their setup documents: <a href="https://devcenter.heroku.com/articles/getting-started-with-python#introduction">Getting Started on Heroku with Django</a>.</p>
</div>

<p>That's all the overview you need in order to get started (see <a href="https://devcenter.heroku.com/articles/how-heroku-works">How Heroku works</a> for a more comprehensive guide).</p>

<h3 id="Creating_an_application_repository_in_Github">Creating an application repository in Github</h3>

<p>Heroku is closely integrated with the <strong>git</strong> source code version control system, using it to upload/synchronise any changes you make to the live system. It does this by adding a new heroku "remote" repository named <em>heroku</em> pointing to a repository for your source on the Heroku cloud. During development you use git to store changes on your own repository. When you want to deploy your site, you sync your changes to the Heroku repository.</p>

<div class="notecard note">
  <p><strong>Note:</strong> If you're used to following good software development practices you are probably already using git or some other SCM system. If you already have a git repository, then you can skip this step.</p>
</div>

<p>There are a lot of ways to work with git, but one of the easiest is to first set up an account on <a href="https://github.com/">Github</a>, create the repository there, and then sync to it locally:</p>

<ol>
 <li>Visit <a href="https://github.com/">https://github.com/</a> and create an account.</li>
 <li>Once you are logged in, click the <strong>+</strong> link in the top toolbar and select <strong>New repository</strong>.</li>
 <li>Fill in all the fields on this form. While these are not compulsory, they are strongly recommended.
  <ul>
   <li>Enter a new repository name (e.g. <em>django_local_library</em>), and description (e.g. "Local Library website written in Django".</li>
   <li>Choose <strong>Python</strong> in the <em>Add .gitignore</em> selection list.</li>
   <li>Choose your preferred license in the <em>Add license</em> selection list.</li>
   <li>Check <strong>Initialize this repository with a README</strong>.</li>
  </ul>
 </li>
 <li>Press <strong>Create repository</strong>.</li>
 <li>Click the green "<strong>Clone or download</strong>" button on your new repo page.</li>
 <li>Copy the URL value from the text field inside the dialog box that appears (it should be something like: <strong>https://github.com/<em>&lt;your_git_user_id&gt;</em>/django_local_library.git</strong>).</li>
</ol>

<p>Now that the repository ("repo") is created we are going to want to clone it on our local computer:</p>

<ol>
 <li>Install <em>git</em> for your local computer (you can find versions for different platforms <a href="https://git-scm.com/downloads">here</a>).</li>
 <li>Open a command prompt/terminal and clone your repository using the URL you copied above:
  <pre class="brush: bash">git clone https://github.com/<strong><em>&lt;your_git_user_id&gt;</em></strong>/django_local_library.git
</pre>
  This will create the repository in a new folder in the current working directory.</li>
 <li>Navigate into the new repo.
  <pre class="brush: bash">cd django_local_library</pre>
 </li>
</ol>

<p>The final steps are to copy your application into this local project directory and then add (or "push", in git lingo) the local repository to your remote Github repository:</p>

<ol>
 <li>Copy your Django application into this folder (all the files at the same level as <strong>manage.py</strong> and below, <strong>not</strong> their containing locallibrary folder).</li>
 <li>Open the <strong>.gitignore</strong> file, copy the following lines into the bottom of it, and then save (this file is used to identify files that should not be uploaded to git by default).
  <pre class="brush: plain"># Text backup files
*.bak

# Database
*.sqlite3</pre>
 </li>
 <li>Open a command prompt/terminal and use the <code>add</code> command to add all files to git. This adds the files which aren't ignored by the <strong>.gitignore</strong> file to the "staging area".
  <pre class="brush: bash">git add -A
</pre>
 </li>
 <li>Use the <code>status</code> command to check that all files you are about to <code>commit</code> are correct (you want to include source files, not binaries, temporary files etc.). It should look a bit like the listing below.
  <pre class="brush: plain">&gt; git status
On branch main
Your branch is up-to-date with 'origin/main'.
Changes to be committed:
  (use "git reset HEAD &lt;file&gt;..." to unstage)

        modified:   .gitignore
        new file:   catalog/__init__.py
        ...
        new file:   catalog/migrations/0001_initial.py
        ...
        new file:   templates/registration/password_reset_form.html</pre>
 </li>
 <li>When you're satisfied, <code>commit</code> the files to your local repository. This is essentially equivalent to signing off on the changes and making them an official part of the local repository.
  <pre class="brush: bash">git commit -m "First version of application moved into github"</pre>
 </li>
 <li>At this point, the remote repository has not been changed. Synchronise (<code>push</code>) your local repository to the remote Github repository using the following command:
  <pre class="brush: bash">git push origin main</pre>
 </li>
</ol>

<div class="notecard warning">
  <p><strong>Warning:</strong> In 2020 Github change the default repo branch name to "main" (from "master"). If using an older/existing repository you might call <code>git push origin master</code> instead.</p>
</div>

<p>When this operation completes, you should be able to go back to the page on Github where you created your repo, refresh the page, and see that your whole application has now been uploaded. You can continue to update your repository as files change using this add/commit/push cycle.</p>

<div class="notecard note">
  <p><strong>Note:</strong> This is a good point to make a backup of your "vanilla" project — while some of the changes we're going to be making in the following sections might be useful for deployment on any platform (or development) others might not.</p>

  <p>The <em>best</em> way to do this is to use <em>git</em> to manage your revisions. With <em>git</em> you can not only go back to a particular old version, but you can maintain this in a separate "branch" from your production changes and cherry-pick any changes to move between production and development branches. <a href="https://help.github.com/articles/good-resources-for-learning-git-and-github/">Learning Git</a> is well worth the effort, but is beyond the scope of this topic.</p>

  <p>The <em>easiest</em> way to do this is to just copy your files into another location. Use whichever approach best matches your knowledge of git!</p>
</div>

<h3 id="Update_the_app_for_Heroku">Update the app for Heroku</h3>

<p>This section explains the changes you'll need to make to our <em>LocalLibrary</em> application to get it to work on Heroku. While Heroku's <a href="https://devcenter.heroku.com/articles/getting-started-with-python#introduction">Getting Started on Heroku with Django</a> instructions assume you will use the Heroku client to also run your local development environment, our changes are compatible with the existing Django development server and the workflows we've already learned.</p>

<h4 id="Procfile">Procfile</h4>

<p>Create the file <code>Procfile</code> (no extension) in the root of your GitHub repository to declare the application's process types and entry points. Copy the following text into it:</p>

<pre class="brush: plain">web: gunicorn locallibrary.wsgi --log-file -</pre>

<p>The "<code>web:</code>" tells Heroku that this is a web dyno and can be sent HTTP traffic. The process to start in this dyno is <em>gunicorn</em>, which is a popular web application server that Heroku recommends. We start Gunicorn using the configuration information in the module <code>locallibrary.wsgi</code> (created with our application skeleton: <strong>/locallibrary/wsgi.py</strong>).</p>

<h4 id="Gunicorn">Gunicorn</h4>

<p><a href="https://gunicorn.org/">Gunicorn</a> is the recommended HTTP server for use with Django on Heroku (as referenced in the Procfile above). It is a pure-Python HTTP server for WSGI applications that can run multiple Python concurrent processes within a single dyno (see <a href="https://devcenter.heroku.com/articles/python-gunicorn">Deploying Python applications with Gunicorn</a> for more information).</p>

<p>While we won't need <em>Gunicorn</em> to serve our LocalLibrary application during development, we'll install it so that it becomes part of our <a href="#requirements">requirements</a> for Heroku to set up on the remote server.</p>

<p>Install <em>Gunicorn</em> locally on the command line using <em>pip</em> (which we installed when <a href="/en-US/docs/Learn/Server-side/Django/development_environment">setting up the development environment</a>):</p>

<div class="notecard note">
  <p><strong>Note:</strong> Make sure that you're in your Python virtual environment (use the <code>workon [name-of-virtual-environment]</code> command) before you install <em>Gunicorn</em> and further modules with <em>pip</em>, or you might experience problems with importing these modules in your <strong>/locallibrary/settings.py</strong> file in the later sections. </p>
</div>

<pre class="brush: bash">pip3 install gunicorn
</pre>

<h4 id="Database_configuration">Database configuration</h4>

<p>We can't use the default SQLite database on Heroku because it is file-based, and it would be deleted from the <em>ephemeral</em> file system every time the application restarts (typically once a day, and every time the application or its configuration variables are changed).</p>

<p>The Heroku mechanism for handling this situation is to use a <a href="https://elements.heroku.com/addons#data-stores">database add-on</a> and configure the web application using information from an environment <a href="https://devcenter.heroku.com/articles/config-vars">configuration variable</a>, set by the add-on. There are quite a lot of database options, but we'll use the <a href="https://devcenter.heroku.com/articles/heroku-postgres-plans#plan-tiers">hobby tier</a> of the <em>Heroku postgres</em> database as this is free, supported by Django, and automatically added to our new Heroku apps when using the free hobby dyno plan tier.</p>

<p>The database connection information is supplied to the web dyno using a configuration variable named <code>DATABASE_URL</code>. Rather than hard-coding this information into Django, Heroku recommends that developers use the <a href="https://warehouse.python.org/project/dj-database-url/">dj-database-url</a> package to parse the <code>DATABASE_URL</code> environment variable and automatically convert it to Django’s desired configuration format. In addition to installing the <em>dj-database-url</em> package we'll also need to install <a href="http://initd.org/psycopg/">psycopg2</a>, as Django needs this to interact with Postgres databases.</p>

<h5 id="dj-database-url_Django_database_configuration_from_environment_variable">dj-database-url (Django database configuration from environment variable)</h5>

<p>Install <em>dj-database-url</em> locally so that it becomes part of our <a href="#requirements">requirements</a> for Heroku to set up on the remote server:</p>

<pre class="brush: bash">$ pip3 install dj-database-url</pre>

<h5 id="settings.py">settings.py</h5>

<p>Open <strong>/locallibrary/settings.py</strong> and copy the following configuration into the bottom of the file:</p>

<pre class="brush: plain"># Heroku: Update database configuration from $DATABASE_URL.
import dj_database_url
db_from_env = dj_database_url.config(conn_max_age=500)
DATABASES['default'].update(db_from_env)</pre>

<div class="notecard note">
  <p><strong>Note:</strong></p>
<ul>
 <li>We'll still be using SQLite during development because the <code>DATABASE_URL</code> environment variable will not be set on our development computer.</li>
 <li>The value <code>conn_max_age=500</code> makes the connection persistent, which is far more efficient than recreating the connection on every request cycle. However, this is optional and can be removed if needed.</li>
</ul>
</div>

<h5 id="psycopg2_Python_Postgres_database_support">psycopg2 (Python Postgres database support)</h5>

<p>Django needs <em>psycopg2</em> to work with Postgres databases and you will need to add this to the <a href="#requirements">requirements.txt</a> for Heroku to set this up on the remote server (as discussed in the requirements section below).</p>

<p>Django will use our SQLite database locally by default, because the <code>DATABASE_URL</code> environment variable isn't set in our local environment. If you want to switch to Postgres completely and use our Heroku free tier database for both development and production then you can. For example, to install psycopg2 and its dependencies locally on a Debian-flavoured Linux system you would use the following Bash/terminal commands:</p>

<pre class="brush: bash">sudo apt-get install python-pip python-dev libpq-dev postgresql postgresql-contrib
pip3 install psycopg2-binary
</pre>

<p>Installation instructions for the other platforms can be found on the <a href="http://initd.org/psycopg/docs/install.html">psycopg2 website here</a>.</p>

<p>However, you don't need to do this — you don't need PostgreSQL active on the local computer, as long as you give it to Heroku as a requirement, in <code>requirements.txt</code> (see below).</p>

<h4 id="Serving_static_files_in_production">Serving static files in production</h4>

<p>During development we used Django and the Django development web server to serve our static files (CSS, JavaScript, etc.). In a production environment we instead typically serve static files from a content delivery network (CDN) or the web server.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Serving static files via Django/web application is inefficient because the requests have to pass through unnecessary additional code (Django) rather than being handled directly by the web server or a completely separate CDN. While this doesn't matter for local use during development, it would have a significant performance impact if we were to use the same approach in production. </p>
</div>

<p>To make it easy to host static files separately from the Django web application, Django provides the <em>collectstatic</em> tool to collect these files for deployment (there is a settings variable that defines where the files should be collected when <em>collectstatic</em> is run). Django templates refer to the hosting location of the static files relative to a settings variable (<code>STATIC_URL</code>), so that this can be changed if the static files are moved to another host/server.</p>

<p>The relevant setting variables are:</p>

<ul>
 <li><code>STATIC_URL</code>: This is the base URL location from which static files will be served, for example on a CDN. This is used for the static template variable that is accessed in our base template (see <a href="/en-US/docs/Learn/Server-side/Django/Home_page">Django Tutorial Part 5: Creating our home page</a>).</li>
 <li><code>STATIC_ROOT</code>: This is the absolute path to a directory where Django's <em>collectstatic</em> tool will gather any static files referenced in our templates. Once collected, these can then be uploaded as a group to wherever the files are to be hosted.</li>
 <li><code>STATICFILES_DIRS</code>: This lists additional directories that Django's <em>collectstatic</em> tool should search for static files.</li>
</ul>

<h5 id="settings.py_2">settings.py</h5>

<p>Open <strong>/locallibrary/settings.py</strong> and copy the following configuration into the bottom of the file. The <code>BASE_DIR</code> should already have been defined in your file (the <code>STATIC_URL</code> may already have been defined within the file when it was created. While it will cause no harm, you might as well delete the duplicate previous reference).</p>

<pre class="brush: python"># Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/3.1/howto/static-files/

# The absolute path to the directory where collectstatic will collect static files for deployment.
STATIC_ROOT = BASE_DIR / 'staticfiles'

# The URL to use when referring to static files (where they will be served from)
STATIC_URL = '/static/'
</pre>

<p>We'll actually do the file serving using a library called <a href="https://warehouse.python.org/project/whitenoise/">WhiteNoise</a>, which we install and configure in the next section.</p>

<p>For more information, see <a href="https://devcenter.heroku.com/articles/django-assets">Django and Static Assets</a> (Heroku docs).</p>

<h4 id="Whitenoise">Whitenoise</h4>

<p>There are many ways to serve static files in production (we saw the relevant Django settings in the previous sections). Heroku recommends using the <a href="https://warehouse.python.org/project/whitenoise/">WhiteNoise</a> project for serving of static assets directly from Gunicorn in production.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Heroku automatically calls <em>collectstatic</em> and prepares your static files for use by WhiteNoise after it uploads your application. Check out <a href="https://warehouse.python.org/project/whitenoise/">WhiteNoise</a> documentation for an explanation of how it works and why the implementation is a relatively efficient method for serving these files.</p>
</div>

<p>The steps to set up <em>WhiteNoise</em> to use with the project are <a href="https://whitenoise.evans.io/en/stable/django.html">given here</a> (and reproduced below):</p>

<h5 id="WhiteNoise">WhiteNoise</h5>

<p>Install whitenoise locally using the following command:</p>

<pre class="brush: bash">$ pip3 install whitenoise
</pre>

<h5 id="settings.py_3">settings.py</h5>

<p>To install <em>WhiteNoise</em> into your Django application, open <strong>/locallibrary/settings.py</strong>, find the <code>MIDDLEWARE</code> setting and add the <code>WhiteNoiseMiddleware</code> near the top of the list, just below the <code>SecurityMiddleware</code>:</p>

<pre class="brush: python">MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
</pre>

<p>Optionally, you can reduce the size of the static files when they are served (this is more efficient). Just add the following to the bottom of <strong>/locallibrary/settings.py</strong>:</p>

<pre class="brush: python"># Simplified static file serving.
# https://warehouse.python.org/project/whitenoise/
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
</pre>

<h4 id="Requirements">Requirements</h4>

<p>The Python requirements of your web application must be stored in a file <strong>requirements.txt</strong> in the root of your repository. Heroku will then install these automatically when it rebuilds your environment. You can create this file using <em>pip</em> on the command line (run the following in the repo root):</p>

<pre class="brush: bash">pip3 freeze &gt; requirements.txt</pre>

<p>After installing all the different dependencies above, your <strong>requirements.txt</strong> file should have <em>at least</em> these items listed (though the version numbers may be different).
Please delete any other dependencies not listed below, unless you've explicitly added them for this application.</p>

<pre class="brush: plain">dj-database-url==0.5.0
Django==3.1.2
gunicorn==20.0.4
psycopg2-binary==2.8.6
whitenoise==5.2.0
</pre>

<div class="notecard note">
  <p><strong>Note:</strong> Make sure that a <strong>psycopg2</strong> line like the one above is present! Even if you didn't install this locally then you should still add it to <strong>requirements.txt</strong>.</p>
</div>

<h4 id="Runtime">Runtime</h4>

<p>The <strong>runtime.txt</strong> file, if defined, tells Heroku which programming language to use. Create the file in the root of the repo and add the following text:</p>

<pre class="brush: plain">python-3.8.6</pre>

<div class="notecard note">
  <p><strong>Note:</strong> Heroku only supports a small number of <a href="https://devcenter.heroku.com/articles/python-support#supported-python-runtimes">Python runtimes</a> (at time of writing, this includes the one above). Heroku will use a supported runtime irrespective of the value specified in this file.</p>
</div>

<h4 id="Re-test_and_save_changes_to_Github">Re-test and save changes to Github</h4>

<p>Before we proceed, lets test the site again locally and make sure it wasn't broken by any of our changes above. Run the development web server as usual and then check the site still works as you expect on your browser.</p>

<pre class="brush: bash">python3 manage.py runserver</pre>

<p>Next, lets <code>push</code> our changes to Github. In the terminal (after having navigated to our local repository), enter the following commands:</p>

<pre class="brush: python">git add -A
git commit -m "Added files and changes required for deployment to heroku"
git push origin main
</pre>

<p>We should now be ready to start deploying LocalLibrary on Heroku.</p>

<h3 id="Get_a_Heroku_account">Get a Heroku account</h3>

<p>To start using Heroku you will first need to create an account:</p>

<ul>
 <li>Go to <a href="https://www.heroku.com/">www.heroku.com</a> and click the <strong>SIGN UP FOR FREE</strong> button.</li>
 <li>Enter your details and then press <strong>CREATE FREE ACCOUNT</strong>. You'll be asked to check your account for a sign-up email.</li>
 <li>Click the account activation link in the signup email. You'll be taken back to your account on the web browser.</li>
 <li>Enter your password and click <strong>SET PASSWORD AND LOGIN</strong>.</li>
 <li>You'll then be logged in and taken to the Heroku dashboard: <a href="https://dashboard.heroku.com/apps">https://dashboard.heroku.com/apps</a>.</li>
</ul>

<h3 id="Install_the_client">Install the client</h3>

<p>Download and install the Heroku client by following the <a href="https://devcenter.heroku.com/articles/getting-started-with-python#set-up">instructions on Heroku here</a>.</p>

<p>After the client is installed you will be able run commands. For example to get help on the client:</p>

<pre class="brush: bash">heroku help
</pre>

<h3 id="Create_and_upload_the_website">Create and upload the website</h3>

<p>To create the app we run the "create" command in the root directory of our repository. This creates a git remote ("pointer to a remote repository") named <em>heroku</em> in our local git environment.</p>

<pre class="brush: bash">heroku create</pre>

<div class="notecard note">
  <p><strong>Note:</strong> You can name the remote if you like by specifying a value after "create". If you don't then you'll get a random name. The name is used in the default URL.</p>
</div>

<p>We can then push our app to the Heroku repository as shown below. This will upload the app, package it in a dyno, run <em>collectstatic</em>, and start the site.</p>

<pre class="brush: bash">git push heroku main</pre>

<p>If we're lucky, the app is now "running" on the site, but it won't be working properly because we haven't set up the database tables for use by our application. To do this we need to use the <code>heroku run</code> command and start a "<a href="https://devcenter.heroku.com/articles/deploying-python#one-off-dynos">one off dyno</a>" to perform a migrate operation. Enter the following command in your terminal:</p>

<pre class="brush: bash">heroku run python manage.py migrate</pre>

<p>We're also going to need to be able to add books and authors, so lets also create our administration superuser, again using a one-off dyno:</p>

<pre class="brush: bash">heroku run python manage.py createsuperuser</pre>

<p>Once this is complete, we can look at the site. It should work, although it won't have any books in it yet. To open your browser to the new website, use the command:</p>

<pre class="brush: bash">heroku open</pre>

<p>Create some books in the admin site, and check out whether the site is behaving as you expect.</p>

<h3 id="Managing_addons">Managing addons</h3>

<p>You can check out the add-ons to your app using the <code>heroku addons</code> command. This will list all addons, and their price tier and state.</p>

<pre class="brush: bash">&gt; heroku addons

Add-on                                     Plan       Price  State
─────────────────────────────────────────  ─────────  ─────  ───────
heroku-postgresql (postgresql-flat-26536)  hobby-dev  free   created
 └─ as DATABASE</pre>

<p>Here we see that we have just one add-on, the postgres SQL database. This is free, and was created automatically when we created the app. You can open a web page to examine the database add-on (or any other add-on) in more detail using the following command:</p>

<pre class="brush: bash">heroku addons:open heroku-postgresql
</pre>

<p>Other commands allow you to create, destroy, upgrade and downgrade addons (using a similar syntax to opening). For more information see <a href="https://devcenter.heroku.com/articles/managing-add-ons">Managing Add-ons</a> (Heroku docs).</p>

<h3 id="Setting_configuration_variables">Setting configuration variables</h3>

<p>You can check out the configuration variables for the site using the <code>heroku config</code> command. Below you can see that we have just one variable, the <code>DATABASE_URL</code> used to configure our database.</p>

<pre class="brush: bash">&gt; heroku config

=== locallibrary Config Vars
DATABASE_URL: postgres://uzfnbcyxidzgrl:j2jkUFDF6OGGqxkgg7Hk3ilbZI@ec2-54-243-201-144.compute-1.amazonaws.com:5432/dbftm4qgh3kda3</pre>

<p>If you recall from the section on <a href="#getting_your_website_ready_to_publish">getting the website ready to publish</a>, we have to set environment variables for <code>DJANGO_SECRET_KEY</code> and <code>DJANGO_DEBUG</code>. Let's do this now.</p>

<div class="notecard note">
  <p><strong>Note:</strong> The secret key needs to be really secret! One way to generate a new key is to use the <a href="https://www.miniwebtool.com/django-secret-key-generator/">Django Secret Key Generator</a>.</p>
</div>

<p>We set <code>DJANGO_SECRET_KEY</code> using the <code>config:set</code> command (as shown below). Remember to use your own secret key!</p>

<pre class="brush: bash">&gt; heroku config:set DJANGO_SECRET_KEY="eu09(ilk6@4sfdofb=b_2ht@vad*$ehh9-)3u_83+y%(+phh&amp;="

Setting DJANGO_SECRET_KEY and restarting locallibrary... done, v7
DJANGO_SECRET_KEY: eu09(ilk6@4sfdofb=b_2ht@vad*$ehh9-)3u_83+y%(+phh
</pre>

<p>We similarly set <code>DJANGO_DEBUG</code>:</p>

<pre class="brush: bash">&gt; heroku config:set DJANGO_DEBUG=False

Setting DJANGO_DEBUG and restarting locallibrary... done, v8
DJANGO_DEBUG: False</pre>

<p>If you visit the site now you'll get a "Bad request" error, because the <a href="https://docs.djangoproject.com/en/3.1/ref/settings/#allowed-hosts">ALLOWED_HOSTS</a> setting is <em>required</em> if you have <code>DEBUG=False</code> (as a security measure). Open <strong>/locallibrary/settings.py</strong> and change the <code>ALLOWED_HOSTS</code> setting to include your base app url (e.g. 'locallibrary1234.herokuapp.com') and the URL you normally use on your local development server.</p>

<pre class="brush: python">ALLOWED_HOSTS = ['&lt;your app URL without the https:// prefix&gt;.herokuapp.com','127.0.0.1']
# For example:
# ALLOWED_HOSTS = ['fathomless-scrubland-30645.herokuapp.com', '127.0.0.1']
</pre>

<p>Then save your settings and commit them to your Github repo and to Heroku:</p>

<pre class="brush: bash">git add -A
git commit -m 'Update ALLOWED_HOSTS with site and development server URL'
git push origin main
git push heroku main</pre>

<div class="notecard note">
  <p><strong>Note:</strong> After the site update to Heroku completes, enter a URL that does not exist (e.g. <strong>/catalog/doesnotexist/</strong>). Previously this would have displayed a detailed debug page, but now you should just see a simple "Not Found" page.</p>
</div>

<h3 id="Debugging">Debugging</h3>

<p>The Heroku client provides a few tools for debugging:</p>

<pre class="brush: bash"># Show current logs
heroku logs

# Show current logs and keep updating with any new results
heroku logs --tail

# Add additional logging for collectstatic (this tool is run automatically during a build)
heroku config:set DEBUG_COLLECTSTATIC=1

# Display dyno status
heroku ps
</pre>

<p>If you need more information than these can provide you will need to start looking into <a href="https://docs.djangoproject.com/en/3.1/topics/logging/">Django Logging</a>.</p>

<h2 id="Summary">Summary</h2>

<p>That's the end of this tutorial on setting up Django apps in production, and also the series of tutorials on working with Django. We hope you've found them useful. You can check out a fully worked-through version of the <a href="https://github.com/mdn/django-locallibrary-tutorial">source code on Github here</a>.<br>
 <br>
 The next step is to read our last few articles, and then complete the assessment task.</p>

<h2 id="See_also">See also</h2>

<ul>
 <li><a href="https://docs.djangoproject.com/en/3.1/howto/deployment/">Deploying Django</a> (Django docs)

  <ul>
   <li><a href="https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/">Deployment checklist</a> (Django docs)</li>
   <li><a href="https://docs.djangoproject.com/en/3.1/howto/static-files/deployment/">Deploying static files</a> (Django docs)</li>
   <li><a href="https://docs.djangoproject.com/en/3.1/howto/deployment/wsgi/">How to deploy with WSGI</a> (Django docs)</li>
   <li><a href="https://docs.djangoproject.com/en/3.1/howto/deployment/wsgi/modwsgi/">How to use Django with Apache and mod_wsgi</a> (Django docs)</li>
   <li><a href="https://docs.djangoproject.com/en/3.1/howto/deployment/wsgi/gunicorn/">How to use Django with Gunicorn</a> (Django docs)</li>
  </ul>
 </li>
 <li>Heroku
  <ul>
   <li><a href="https://devcenter.heroku.com/articles/django-app-configuration">Configuring Django apps for Heroku</a> (Heroku docs)</li>
   <li><a href="https://devcenter.heroku.com/articles/getting-started-with-python#introduction">Getting Started on Heroku with Django</a> (Heroku docs)</li>
   <li><a href="https://devcenter.heroku.com/articles/django-assets">Django and Static Assets</a> (Heroku docs)</li>
   <li><a href="https://devcenter.heroku.com/articles/python-concurrency-and-database-connections">Concurrency and Database Connections in Django</a> (Heroku docs)</li>
   <li><a href="https://devcenter.heroku.com/articles/how-heroku-works">How Heroku works</a> (Heroku docs)</li>
   <li><a href="https://devcenter.heroku.com/articles/dynos">Dynos and the Dyno Manager</a> (Heroku docs)</li>
   <li><a href="https://devcenter.heroku.com/articles/config-vars">Configuration and Config Vars</a> (Heroku docs)</li>
   <li><a href="https://devcenter.heroku.com/articles/limits">Limits</a> (Heroku docs)</li>
   <li><a href="https://devcenter.heroku.com/articles/python-gunicorn">Deploying Python applications with Gunicorn</a> (Heroku docs)</li>
   <li><a href="https://devcenter.heroku.com/articles/deploying-python">Deploying Python and Django apps on Heroku</a> (Heroku docs)</li>
   <li><a href="https://devcenter.heroku.com/search?q=django">Other Heroku Django docs</a></li>
  </ul>
 </li>
 <li>Digital Ocean
  <ul>
   <li><a href="https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-uwsgi-and-nginx-on-ubuntu-16-04">How To Serve Django Applications with uWSGI and Nginx on Ubuntu 16.04</a></li>
   <li><a href="https://www.digitalocean.com/community/tutorials?q=django">Other Digital Ocean Django community docs</a></li>
  </ul>
 </li>
</ul>

<p>{{PreviousMenuNext("Learn/Server-side/Django/Testing", "Learn/Server-side/Django/web_application_security", "Learn/Server-side/Django")}}</p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Introduction">Django introduction</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/development_environment">Setting up a Django development environment</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Tutorial_local_library_website">Django Tutorial: The Local Library website</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/skeleton_website">Django Tutorial Part 2: Creating a skeleton website</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Models">Django Tutorial Part 3: Using models</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Admin_site">Django Tutorial Part 4: Django admin site</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Home_page">Django Tutorial Part 5: Creating our home page</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Generic_views">Django Tutorial Part 6: Generic list and detail views</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Sessions">Django Tutorial Part 7: Sessions framework</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Authentication">Django Tutorial Part 8: User authentication and permissions</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Forms">Django Tutorial Part 9: Working with forms</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Testing">Django Tutorial Part 10: Testing a Django web application</a></li>
 <li><strong>Django Tutorial Part 11: Deploying Django to production</strong></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/web_application_security">Django web application security</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/django_assessment_blog">DIY Django mini blog</a></li>
</ul>