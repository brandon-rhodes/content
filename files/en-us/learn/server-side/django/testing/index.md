---
title: 'Django Tutorial Part 10: Testing a Django web application'
slug: Learn/Server-side/Django/Testing
tags:
  - Beginner
  - CodingScripting
  - Django Testing
  - Testing
  - Tutorial
  - django
  - server-side
  - tests
  - unit tests
---
<div>{{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/Server-side/Django/Forms", "Learn/Server-side/Django/Deployment", "Learn/Server-side/Django")}}</div>

<p>As websites grow they become harder to test manually. Not only is there more to test, but, as interactions between components become more complex, a small change in one area can impact other areas, so more changes will be required to ensure everything keeps working and errors are not introduced as more changes are made. One way to mitigate these problems is to write automated tests, which can easily and reliably be run every time you make a change. This tutorial shows how to automate <em>unit testing</em> of your website using Django's test framework.</p>

<table>
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td>Complete all previous tutorial topics, including <a href="/en-US/docs/Learn/Server-side/Django/Forms">Django Tutorial Part 9: Working with forms</a>.</td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To understand how to write unit tests for Django-based websites.</td>
  </tr>
 </tbody>
</table>

<h2 id="Overview">Overview</h2>

<p>The <a href="/en-US/docs/Learn/Server-side/Django/Tutorial_local_library_website">Local Library</a> currently has pages to display lists of all books and authors, detail views for <code>Book</code> and <code>Author</code> items, a page to renew <code>BookInstance</code>s, and pages to create, update, and delete <code>Author</code> items (and <code>Book</code> records too, if you completed the <em>challenge</em> in the <a href="/en-US/docs/Learn/Server-side/Django/Forms">forms tutorial</a>). Even with this relatively small site, manually navigating to each page and <em>superficially</em> checking that everything works as expected can take several minutes. As we make changes and grow the site, the time required to manually check that everything works "properly" will only grow. If we were to continue as we are, eventually we'd be spending most of our time testing, and very little time improving our code.</p>

<p>Automated tests can really help with this problem! The obvious benefits are that they can be run much faster than manual tests, can test to a much lower level of detail, and test exactly the same functionality every time (human testers are nowhere near as reliable!) Because they are fast, automated tests can be executed more regularly, and if a test fails, they point to exactly where code is not performing as expected.</p>

<p>In addition, automated tests can act as the first real-world "user" of your code, forcing you to be rigorous about defining and documenting how your website should behave. Often they are the basis for your code examples and documentation. For these reasons, some software development processes start with test definition and implementation, after which the code is written to match the required behavior (e.g. <a href="https://en.wikipedia.org/wiki/Test-driven_development">test-driven</a> and <a href="https://en.wikipedia.org/wiki/Behavior-driven_development">behavior-driven</a> development).</p>

<p>This tutorial shows how to write automated tests for Django, by adding a number of tests to the <em>LocalLibrary</em> website.</p>

<h3 id="Types_of_testing">Types of testing</h3>

<p>There are numerous types, levels, and classifications of tests and testing approaches. The most important automated tests are:</p>

<dl>
 <dt>Unit tests</dt>
 <dd>Verify functional behavior of individual components, often to class and function level.</dd>
 <dt>Regression tests</dt>
 <dd>Tests that reproduce historic bugs. Each test is initially run to verify that the bug has been fixed, and then re-run to ensure that it has not been reintroduced following later changes to the code.</dd>
 <dt>Integration tests</dt>
 <dd>Verify how groupings of components work when used together. Integration tests are aware of the required interactions between components, but not necessarily of the internal operations of each component. They may cover simple groupings of components through to the whole website.</dd>
</dl>

<div class="notecard note">
  <p><strong>Note:</strong> Other common types of tests include black box, white box, manual, automated, canary, smoke, conformance, acceptance, functional, system, performance, load, and stress tests. Look them up for more information.</p>
</div>

<h3 id="What_does_Django_provide_for_testing">What does Django provide for testing?</h3>

<p>Testing a website is a complex task, because it is made of several layers of logic – from HTTP-level request handling, queries models, to form validation and processing, and template rendering.</p>

<p>Django provides a test framework with a small hierarchy of classes that build on the Python standard <code><a href="https://docs.python.org/3/library/unittest.html#module-unittest" title="(in Python v3.5)">unittest</a></code> library. Despite the name, this test framework is suitable for both unit and integration tests. The Django framework adds API methods and tools to help test web and Django-specific behavior. These allow you to simulate requests, insert test data, and inspect your application's output. Django also provides an API (<a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/#liveservertestcase">LiveServerTestCase</a>) and tools for <a href="https://docs.djangoproject.com/en/3.1/topics/testing/advanced/#other-testing-frameworks">using different testing frameworks</a>, for example you can integrate with the popular <a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Your_own_automation_environment">Selenium</a> framework to simulate a user interacting with a live browser.</p>

<p>To write a test you derive from any of the Django (or <em>unittest</em>) test base classes (<a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/#simpletestcase">SimpleTestCase</a>, <a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/#transactiontestcase">TransactionTestCase</a>, <a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/#testcase">TestCase</a>, <a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/#liveservertestcase">LiveServerTestCase</a>) and then write separate methods to check that specific functionality works as expected (tests use "assert" methods to test that expressions result in <code>True</code> or <code>False</code> values, or that two values are equal, etc.) When you start a test run, the framework executes the chosen test methods in your derived classes. The test methods are run independently, with common setup and/or tear-down behavior defined in the class, as shown below.</p>

<pre class="brush: python">class YourTestClass(TestCase):
    def setUp(self):
        # Setup run before every test method.
        pass

    def tearDown(self):
        # Clean up run after every test method.
        pass

    def test_something_that_will_pass(self):
        self.assertFalse(False)

    def test_something_that_will_fail(self):
        self.assertTrue(False)
</pre>

<p>The best base class for most tests is <a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/#testcase">django.test.TestCase</a>. This test class creates a clean database before its tests are run, and runs every test function in its own transaction. The class also owns a test <a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/#django.test.Client" title="django.test.Client">Client</a> that you can use to simulate a user interacting with the code at the view level. In the following sections we're going to concentrate on unit tests, created using this <a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/#testcase">TestCase</a> base class.</p>

<div class="notecard note">
  <p><strong>Note:</strong> The <a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/#testcase">django.test.TestCase</a> class is very convenient, but may result in some tests being slower than they need to be (not every test will need to set up its own database or simulate the view interaction). Once you're familiar with what you can do with this class, you may want to replace some of your tests with the available simpler test classes.</p>
</div>

<h3 id="What_should_you_test">What should you test?</h3>

<p>You should test all aspects of your own code, but not any libraries or functionality provided as part of Python or Django.</p>

<p>So for example, consider the <code>Author</code> model defined below. You don't need to explicitly test that <code>first_name</code> and <code>last_name</code> have been stored properly as <code>CharField</code> in the database because that is something defined by Django (though of course in practice you will inevitably test this functionality during development). Nor do you need to test that the <code>date_of_birth</code> has been validated to be a date field, because that is again something implemented in Django.</p>

<p>However you should check the text used for the labels (<em>First name, Last name, Date of birth, Died</em>), and the size of the field allocated for the text (<em>100 chars</em>), because these are part of your design and something that could be broken/changed in future.</p>

<pre class="brush: python">class Author(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    date_of_birth = models.DateField(null=True, blank=True)
    date_of_death = models.DateField('Died', null=True, blank=True)

    def get_absolute_url(self):
        return reverse('author-detail', args=[str(self.id)])

    def __str__(self):
        return '%s, %s' % (self.last_name, self.first_name)</pre>

<p>Similarly, you should check that the custom methods <code>get_absolute_url()</code> and <code>__str__()</code> behave as required because they are your code/business logic. In the case of <code>get_absolute_url()</code> you can trust that the Django <code>reverse()</code> method has been implemented properly, so what you're testing is that the associated view has actually been defined.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Astute readers may note that we would also want to constrain the date of birth and death to sensible values, and check that death comes after birth. In Django this constraint would be added to your form classes (although you can define validators for model fields and model validators these are only used at the form level if they are called by the model's clean() method. This requires a ModelForm or the model's clean() method needs to be specifically called.)</p>
</div>

<p>With that in mind let's start looking at how to define and run tests.</p>

<h2 id="Test_structure_overview">Test structure overview</h2>

<p>Before we go into the detail of "what to test", let's first briefly look at <em>where</em> and <em>how</em> tests are defined.</p>

<p>Django uses the unittest module’s <a href="https://docs.python.org/3/library/unittest.html#unittest-test-discovery" title="(in Python v3.5)">built-in test discovery</a>, which will discover tests under the current working directory in any file named with the pattern <strong>test*.py</strong>. Provided you name the files appropriately, you can use any structure you like. We recommend that you create a module for your test code, and have separate files for models, views, forms, and any other types of code you need to test. For example:</p>

<pre class="brush: plain">catalog/
  /tests/
    __init__.py
    test_models.py
    test_forms.py
    test_views.py
</pre>

<p>Create a file structure as shown above in your <em>LocalLibrary</em> project. The <strong>__init__.py</strong> should be an empty file (this tells Python that the directory is a package). You can create the three test files by copying and renaming the skeleton test file <strong>/catalog/tests.py</strong>.</p>

<div class="notecard note">
  <p><strong>Note:</strong> The skeleton test file <strong>/catalog/tests.py</strong> was created automatically when we <a href="/en-US/docs/Learn/Server-side/Django/skeleton_website">built the Django skeleton website</a>. It is perfectly "legal" to put all your tests inside it, but if you test properly, you'll quickly end up with a very large and unmanageable test file.</p>

  <p>Delete the skeleton file as we won't need it.</p>
</div>

<p>Open <strong>/catalog/tests/test_models.py</strong>. The file should import <code>django.test.TestCase</code>, as shown:</p>

<pre class="brush: python">from django.test import TestCase

# Create your tests here.
</pre>

<p>Often you will add a test class for each model/view/form you want to test, with individual methods for testing specific functionality. In other cases you may wish to have a separate class for testing a specific use case, with individual test functions that test aspects of that use-case (for example, a class to test that a model field is properly validated, with functions to test each of the possible failure cases). Again, the structure is very much up to you, but it is best if you are consistent.</p>

<p>Add the test class below to the bottom of the file. The class demonstrates how to construct a test case class by deriving from <code>TestCase</code>.</p>

<pre class="brush: python">class YourTestClass(TestCase):
    @classmethod
    def setUpTestData(cls):
        print("setUpTestData: Run once to set up non-modified data for all class methods.")
        pass

    def setUp(self):
        print("setUp: Run once for every test method to setup clean data.")
        pass

    def test_false_is_false(self):
        print("Method: test_false_is_false.")
        self.assertFalse(False)

    def test_false_is_true(self):
        print("Method: test_false_is_true.")
        self.assertTrue(False)

    def test_one_plus_one_equals_two(self):
        print("Method: test_one_plus_one_equals_two.")
        self.assertEqual(1 + 1, 2)</pre>

<p>The new class defines two methods that you can use for pre-test configuration (for example, to create any models or other objects you will need for the test):</p>

<ul>
 <li><code>setUpTestData()</code> is called once at the beginning of the test run for class-level setup. You'd use this to create objects that aren't going to be modified or changed in any of the test methods.</li>
 <li><code>setUp()</code> is called before every test function to set up any objects that may be modified by the test (every test function will get a "fresh" version of these objects).</li>
</ul>

<div class="notecard note">
  <p><strong>Note:</strong> The test classes also have a <code>tearDown()</code> method which we haven't used. This method isn't particularly useful for database tests, since the <code>TestCase</code> base class takes care of database teardown for you.</p>
</div>

<p>Below those we have a number of test methods, which use <code>Assert</code> functions to test whether conditions are true, false or equal (<code>AssertTrue</code>, <code>AssertFalse</code>, <code>AssertEqual</code>). If the condition does not evaluate as expected then the test will fail and report the error to your console.</p>

<p>The <code>AssertTrue</code>, <code>AssertFalse</code>, <code>AssertEqual</code> are standard assertions provided by <strong>unittest</strong>. There are other standard assertions in the framework, and also <a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/#assertions">Django-specific assertions</a> to test if a view redirects (<code>assertRedirects</code>), to test if a particular template has been used (<code>assertTemplateUsed</code>), etc.</p>

<div class="notecard note">
  <p><strong>Note:</strong> You should <strong>not</strong> normally include <strong>print()</strong> functions in your tests as shown above. We do that here only so that you can see the order that the setup functions are called in the console (in the following section).</p>
</div>

<h2 id="How_to_run_the_tests">How to run the tests</h2>

<p>The easiest way to run all the tests is to use the command:</p>

<pre class="brush: bash">python3 manage.py test</pre>

<p>This will discover all files named with the pattern <strong>test*.py</strong> under the current directory and run all tests defined using appropriate base classes (here we have a number of test files, but only <strong>/catalog/tests/test_models.py</strong> currently contains any tests.) By default the tests will individually report only on test failures, followed by a test summary.</p>

<div class="notecard note">
  <p><strong>Note:</strong> If you get errors similar to: <code>ValueError: Missing staticfiles manifest entry ...</code> this may be because testing does not run <em>collectstatic</em> by default and your app is using a storage class that requires it (see <a href="https://docs.djangoproject.com/en/3.1/ref/contrib/staticfiles/#django.contrib.staticfiles.storage.ManifestStaticFilesStorage.manifest_strict">manifest_strict</a> for more information). There are a number of ways you can overcome this problem - the easiest is to run <em>collectstatic</em> before running the tests:</p>

    <pre class="brush: bash">python3 manage.py collectstatic</pre>
</div>

<p>Run the tests in the root directory of <em>LocalLibrary</em>. You should see an output like the one below.</p>

<pre class="brush: bash">&gt; python3 manage.py test

Creating test database for alias 'default'...
setUpTestData: Run once to set up non-modified data for all class methods.
setUp: Run once for every test method to setup clean data.
Method: test_false_is_false.
setUp: Run once for every test method to setup clean data.
Method: test_false_is_true.
setUp: Run once for every test method to setup clean data.
Method: test_one_plus_one_equals_two.
.
======================================================================
FAIL: test_false_is_true (catalog.tests.tests_models.YourTestClass)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "D:\Github\django_tmp\library_w_t_2\locallibrary\catalog\tests\tests_models.py", line 22, in test_false_is_true
    self.assertTrue(False)
AssertionError: False is not true

----------------------------------------------------------------------
Ran 3 tests in 0.075s

FAILED (failures=1)
Destroying test database for alias 'default'...</pre>

<p>Here we see that we had one test failure, and we can see exactly what function failed and why (this failure is expected, because <code>False</code> is not <code>True</code>!).</p>

<div class="notecard note">
  <p><strong>Note:</strong> The most important thing to learn from the test output above is that it is much more valuable if you use descriptive/informative names for your objects and methods.</p>
</div>

<p>The text shown in <strong>bold</strong> above would not normally appear in the test output (this is generated by the <code>print()</code> functions in our tests). This shows how the <code>setUpTestData()</code> method is called once for the class and <code>setUp()</code> is called before each method.</p>

<p>The next sections show how you can run specific tests, and how to control how much information the tests display.</p>

<h3 id="Showing_more_test_information">Showing more test information</h3>

<p>If you want to get more information about the test run you can change the <em>verbosity</em>. For example, to list the test successes as well as failures (and a whole bunch of information about how the testing database is set up) you can set the verbosity to "2" as shown:</p>

<pre class="brush: bash">python3 manage.py test --verbosity 2</pre>

<p>The allowed verbosity levels are 0, 1, 2, and 3, with the default being "1".</p>

<h3 id="Running_specific_tests">Running specific tests</h3>

<p>If you want to run a subset of your tests you can do so by specifying the full dot path to the package(s), module, <code>TestCase</code> subclass or method:</p>

<pre class="brush: bash"># Run the specified module
python3 manage.py test catalog.tests

# Run the specified module
python3 manage.py test catalog.tests.test_models

# Run the specified class
python3 manage.py test catalog.tests.test_models.YourTestClass

# Run the specified method
python3 manage.py test catalog.tests.test_models.YourTestClass.test_one_plus_one_equals_two
</pre>

<h2 id="LocalLibrary_tests">LocalLibrary tests</h2>

<p>Now we know how to run our tests and what sort of things we need to test, let's look at some practical examples.</p>

<div class="notecard note">
  <p><strong>Note:</strong> We won't write every possible test, but this should give you an idea of how tests work, and what more you can do.</p>
</div>

<h3 id="Models">Models</h3>

<p>As discussed above, we should test anything that is part of our design or that is defined by code that we have written, but not libraries/code that is already tested by Django or the Python development team.</p>

<p>For example, consider the <code>Author</code> model below. Here we should test the labels for all the fields, because even though we haven't explicitly specified most of them, we have a design that says what these values should be. If we don't test the values, then we don't know that the field labels have their intended values. Similarly while we trust that Django will create a field of the specified length, it is worthwhile to specify a test for this length to ensure that it was implemented as planned.</p>

<pre class="brush: python">class Author(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    date_of_birth = models.DateField(null=True, blank=True)
    date_of_death = models.DateField('Died', null=True, blank=True)

    def get_absolute_url(self):
        return reverse('author-detail', args=[str(self.id)])

    def __str__(self):
        return f'{self.last_name}, {self.first_name}'</pre>

<p>Open our <strong>/catalog/tests/test_models.py</strong>, and replace any existing code with the following test code for the <code>Author</code> model.</p>

<p>Here you'll see that we first import <code>TestCase</code> and derive our test class (<code>AuthorModelTest</code>) from it, using a descriptive name so we can easily identify any failing tests in the test output. We then call <code>setUpTestData()</code> to create an author object that we will use but not modify in any of the tests.</p>

<pre class="brush: python">from django.test import TestCase

from catalog.models import Author

class AuthorModelTest(TestCase):
    @classmethod
    def setUpTestData(cls):
        # Set up non-modified objects used by all test methods
        Author.objects.create(first_name='Big', last_name='Bob')

    def test_first_name_label(self):
        author = Author.objects.get(id=1)
        field_label = author._meta.get_field('first_name').verbose_name
        self.assertEqual(field_label, 'first name')

    def test_date_of_death_label(self):
        author = Author.objects.get(id=1)
        field_label = author._meta.get_field('date_of_death').verbose_name
        self.assertEqual(field_label, 'died')

    def test_first_name_max_length(self):
        author = Author.objects.get(id=1)
        max_length = author._meta.get_field('first_name').max_length
        self.assertEqual(max_length, 100)

    def test_object_name_is_last_name_comma_first_name(self):
        author = Author.objects.get(id=1)
        expected_object_name = f'{author.last_name}, {author.first_name}'
        self.assertEqual(str(author), expected_object_name)

    def test_get_absolute_url(self):
        author = Author.objects.get(id=1)
        # This will also fail if the urlconf is not defined.
        self.assertEqual(author.get_absolute_url(), '/catalog/author/1')</pre>

<p>The field tests check that the values of the field labels (<code>verbose_name</code>) and that the size of the character fields are as expected. These methods all have descriptive names, and follow the same pattern:</p>

<pre class="brush: python"># Get an author object to test
author = Author.objects.get(id=1)

# Get the metadata for the required field and use it to query the required field data
field_label = author._meta.get_field('first_name').verbose_name

# Compare the value to the expected result
self.assertEqual(field_label, 'first name')</pre>

<p>The interesting things to note are:</p>

<ul>
 <li>We can't get the <code>verbose_name</code> directly using <code>author.first_name.verbose_name</code>, because <code>author.first_name</code> is a <em>string</em> (not a handle to the <code>first_name</code> object that we can use to access its properties). Instead we need to use the author's <code>_meta</code> attribute to get an instance of the field and use that to query for the additional information.</li>
 <li>We chose to use <code>assertEqual(field_label,'first name')</code> rather than <code>assertTrue(field_label == 'first name')</code>. The reason for this is that if the test fails the output for the former tells you what the label actually was, which makes debugging the problem just a little easier.</li>
</ul>

<div class="notecard note">
  <p><strong>Note:</strong> Tests for the <code>last_name</code> and <code>date_of_birth</code> labels, and also the test for the length of the <code>last_name</code> field have been omitted. Add your own versions now, following the naming conventions and approaches shown above.</p>
</div>

<p>We also need to test our custom methods. These essentially just check that the object name was constructed as we expected using "Last Name", "First Name" format, and that the URL we get for an <code>Author</code> item is as we would expect.</p>

<pre class="brush: python">def test_object_name_is_last_name_comma_first_name(self):
    author = Author.objects.get(id=1)
    expected_object_name = f'{author.last_name}, {author.first_name}'
    self.assertEqual(str(author), expected_object_name)

def test_get_absolute_url(self):
    author = Author.objects.get(id=1)
    # This will also fail if the urlconf is not defined.
    self.assertEqual(author.get_absolute_url(), '/catalog/author/1')</pre>

<p>Run the tests now. If you created the Author model as we described in the models tutorial it is quite likely that you will get an error for the <code>date_of_death</code> label as shown below. The test is failing because it was written expecting the label definition to follow Django's convention of not capitalising the first letter of the label (Django does this for you).</p>

<pre class="brush: bash">======================================================================
FAIL: test_date_of_death_label (catalog.tests.test_models.AuthorModelTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "D:\...\locallibrary\catalog\tests\test_models.py", line 32, in test_date_of_death_label
    self.assertEqual(field_label,'died')
AssertionError: 'Died' != 'died'
- Died
? ^
+ died
? ^</pre>

<p>This is a very minor bug, but it does highlight how writing tests can more thoroughly check any assumptions you may have made.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Change the label for the date_of_death field (/catalog/models.py) to "died" and re-run the tests.</p>
</div>

<p>The patterns for testing the other models are similar so we won't continue to discuss these further. Feel free to create your own tests for our other models.</p>

<h3 id="Forms">Forms</h3>

<p>The philosophy for testing your forms is the same as for testing your models; you need to test anything that you've coded or your design specifies, but not the behavior of the underlying framework and other third party libraries.</p>

<p>Generally this means that you should test that the forms have the fields that you want, and that these are displayed with appropriate labels and help text. You don't need to verify that Django validates the field type correctly (unless you created your own custom field and validation) — i.e. you don't need to test that an email field only accepts emails. However you would need to test any additional validation that you expect to be performed on the fields and any messages that your code will generate for errors.</p>

<p>Consider our form for renewing books. This has just one field for the renewal date, which will have a label and help text that we will need to verify.</p>

<pre class="brush: python">class RenewBookForm(forms.Form):
    """Form for a librarian to renew books."""
    renewal_date = forms.DateField(help_text="Enter a date between now and 4 weeks (default 3).")

    def clean_renewal_date(self):
        data = self.cleaned_data['renewal_date']

        # Check if a date is not in the past.
        if data &lt; datetime.date.today():
            raise ValidationError(_('Invalid date - renewal in past'))

        # Check if date is in the allowed range (+4 weeks from today).
        if data &gt; datetime.date.today() + datetime.timedelta(weeks=4):
            raise ValidationError(_('Invalid date - renewal more than 4 weeks ahead'))

        # Remember to always return the cleaned data.
        return data</pre>

<p>Open our <strong>/catalog/tests/test_forms.py</strong> file and replace any existing code with the following test code for the <code>RenewBookForm</code> form. We start by importing our form and some Python and Django libraries to help test time-related functionality. We then declare our form test class in the same way as we did for models, using a descriptive name for our <code>TestCase</code>-derived test class.</p>

<pre class="brush: python">import datetime

from django.test import TestCase
from django.utils import timezone

from catalog.forms import RenewBookForm

class RenewBookFormTest(TestCase):
    def test_renew_form_date_field_label(self):
        form = RenewBookForm()
        self.assertTrue(form.fields['renewal_date'].label is None or form.fields['renewal_date'].label == 'renewal date')

    def test_renew_form_date_field_help_text(self):
        form = RenewBookForm()
        self.assertEqual(form.fields['renewal_date'].help_text, 'Enter a date between now and 4 weeks (default 3).')

    def test_renew_form_date_in_past(self):
        date = datetime.date.today() - datetime.timedelta(days=1)
        form = RenewBookForm(data={'renewal_date': date})
        self.assertFalse(form.is_valid())

    def test_renew_form_date_too_far_in_future(self):
        date = datetime.date.today() + datetime.timedelta(weeks=4) + datetime.timedelta(days=1)
        form = RenewBookForm(data={'renewal_date': date})
        self.assertFalse(form.is_valid())

    def test_renew_form_date_today(self):
        date = datetime.date.today()
        form = RenewBookForm(data={'renewal_date': date})
        self.assertTrue(form.is_valid())

    def test_renew_form_date_max(self):
        date = timezone.localtime() + datetime.timedelta(weeks=4)
        form = RenewBookForm(data={'renewal_date': date})
        self.assertTrue(form.is_valid())
</pre>

<p>The first two functions test that the field's <code>label</code> and <code>help_text</code> are as expected. We have to access the field using the fields dictionary (e.g. <code>form.fields['renewal_date']</code>). Note here that we also have to test whether the label value is <code>None</code>, because even though Django will render the correct label it returns <code>None</code> if the value is not <em>explicitly</em> set.</p>

<p>The rest of the functions test that the form is valid for renewal dates just inside the acceptable range and invalid for values outside the range. Note how we construct test date values around our current date (<code>datetime.date.today()</code>) using <code>datetime.timedelta()</code> (in this case specifying a number of days or weeks). We then just create the form, passing in our data, and test if it is valid.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Here we don't actually use the database or test client. Consider modifying these tests to use <a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/#django.test.SimpleTestCase">SimpleTestCase</a>.</p>

  <p>We also need to validate that the correct errors are raised if the form is invalid, however this is usually done as part of view processing, so we'll take care of that in the next section.</p>
</div>

<p>That's all for forms; we do have some others, but they are automatically created by our generic class-based editing views, and should be tested there! Run the tests and confirm that our code still passes!</p>

<h3 id="Views">Views</h3>

<p>To validate our view behavior we use the Django test <a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/#django.test.Client">Client</a>. This class acts like a dummy web browser that we can use to simulate <code>GET</code> and <code>POST</code> requests on a URL and observe the response. We can see almost everything about the response, from low-level HTTP (result headers and status codes) through to the template we're using to render the HTML and the context data we're passing to it. We can also see the chain of redirects (if any) and check the URL and status code at each step. This allows us to verify that each view is doing what is expected.</p>

<p>Let's start with one of our simplest views, which provides a list of all Authors. This is displayed at URL <strong>/catalog/authors/</strong> (an URL named 'authors' in the URL configuration).</p>

<pre class="brush: python">class AuthorListView(generic.ListView):
    model = Author
    paginate_by = 10
</pre>

<p>As this is a generic list view almost everything is done for us by Django. Arguably if you trust Django then the only thing you need to test is that the view is accessible at the correct URL and can be accessed using its name. However if you're using a test-driven development process you'll start by writing tests that confirm that the view displays all Authors, paginating them in lots of 10.</p>

<p>Open the <strong>/catalog/tests/test_views.py</strong> file and replace any existing text with the following test code for <code>AuthorListView</code>. As before we import our model and some useful classes. In the <code>setUpTestData()</code> method we set up a number of <code>Author</code> objects so that we can test our pagination.</p>

<pre class="brush: python">from django.test import TestCase
from django.urls import reverse

from catalog.models import Author

class AuthorListViewTest(TestCase):
    @classmethod
    def setUpTestData(cls):
        # Create 13 authors for pagination tests
        number_of_authors = 13

        for author_id in range(number_of_authors):
            Author.objects.create(
                first_name=f'Christian {author_id}',
                last_name=f'Surname {author_id}',
            )

    def test_view_url_exists_at_desired_location(self):
        response = self.client.get('/catalog/authors/')
        self.assertEqual(response.status_code, 200)

    def test_view_url_accessible_by_name(self):
        response = self.client.get(reverse('authors'))
        self.assertEqual(response.status_code, 200)

    def test_view_uses_correct_template(self):
        response = self.client.get(reverse('authors'))
        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, 'catalog/author_list.html')

    def test_pagination_is_ten(self):
        response = self.client.get(reverse('authors'))
        self.assertEqual(response.status_code, 200)
        self.assertTrue('is_paginated' in response.context)
        self.assertTrue(response.context['is_paginated'] == True)
        self.assertEqual(len(response.context['author_list']), 10)

    def test_lists_all_authors(self):
        # Get second page and confirm it has (exactly) remaining 3 items
        response = self.client.get(reverse('authors')+'?page=2')
        self.assertEqual(response.status_code, 200)
        self.assertTrue('is_paginated' in response.context)
        self.assertTrue(response.context['is_paginated'] == True)
        self.assertEqual(len(response.context['author_list']), 3)</pre>

<p>All the tests use the client (belonging to our <code>TestCase</code>'s derived class) to simulate a <code>GET</code> request and get a response. The first version checks a specific URL (note, just the specific path without the domain) while the second generates the URL from its name in the URL configuration.</p>

<pre class="brush: python">response = self.client.get('/catalog/authors/')
response = self.client.get(reverse('authors'))
</pre>

<p>Once we have the response we query it for its status code, the template used, whether or not the response is paginated, the number of items returned, and the total number of items.</p>

<div class="notecard note">
    <p><strong>Note:</strong> If you set the <code>paginate_by</code> variable in your <strong>/catalog/views.py</strong> file to a number other than 10, make sure to update the lines that test that the correct number of items are displayed in paginated templates above and in following sections. For example, if you set the variable for the author list page to 5, update the line above to:</p>

    <pre class="brush: python">self.assertTrue(len(response.context['author_list']) == 5)</pre>
</div>

<p>The most interesting variable we demonstrate above is <code>response.context</code>, which is the context variable passed to the template by the view.
This is incredibly useful for testing, because it allows us to confirm that our template is getting all the data it needs. In other words we can check that we're using the intended template and what data the template is getting, which goes a long way to verifying that any rendering issues are solely due to template.</p>

<h4 id="Views_that_are_restricted_to_logged_in_users">Views that are restricted to logged in users</h4>

<p>In some cases you'll want to test a view that is restricted to just logged in users. For example our <code>LoanedBooksByUserListView</code> is very similar to our previous view but is only available to logged in users, and only displays <code>BookInstance</code> records that are borrowed by the current user, have the 'on loan' status, and are ordered "oldest first".</p>

<pre class="brush: python">from django.contrib.auth.mixins import LoginRequiredMixin

class LoanedBooksByUserListView(LoginRequiredMixin, generic.ListView):
    """Generic class-based view listing books on loan to current user."""
    model = BookInstance
    template_name ='catalog/bookinstance_list_borrowed_user.html'
    paginate_by = 10

    def get_queryset(self):
        return BookInstance.objects.filter(borrower=self.request.user).filter(status__exact='o').order_by('due_back')</pre>

<p>Add the following test code to <strong>/catalog/tests/test_views.py</strong>. Here we first use <code>SetUp()</code> to create some user login accounts and <code>BookInstance</code> objects (along with their associated books and other records) that we'll use later in the tests. Half of the books are borrowed by each test user, but we've initially set the status of all books to "maintenance". We've used <code>SetUp()</code> rather than <code>setUpTestData()</code> because we'll be modifying some of these objects later.</p>

<div class="notecard note">
  <p><strong>Note:</strong> The <code>setUp()</code> code below creates a book with a specified <code>Language</code>, but <em>your</em> code may not include the <code>Language</code> model as this was created as a <em>challenge</em>. If this is the case, comment out the parts of the code that create or import Language objects. You should also do this in the <code>RenewBookInstancesViewTest</code> section that follows.</p>
</div>

<pre class="brush: python">import datetime

from django.utils import timezone
from django.contrib.auth.models import User # Required to assign User as a borrower

from catalog.models import BookInstance, Book, Genre, Language

class LoanedBookInstancesByUserListViewTest(TestCase):
    def setUp(self):
        # Create two users
        test_user1 = User.objects.create_user(username='testuser1', password='1X&lt;ISRUkw+tuK')
        test_user2 = User.objects.create_user(username='testuser2', password='2HJ1vRV0Z&amp;3iD')

        test_user1.save()
        test_user2.save()

        # Create a book
        test_author = Author.objects.create(first_name='John', last_name='Smith')
        test_genre = Genre.objects.create(name='Fantasy')
        test_language = Language.objects.create(name='English')
        test_book = Book.objects.create(
            title='Book Title',
            summary='My book summary',
            isbn='ABCDEFG',
            author=test_author,
            language=test_language,
        )

        # Create genre as a post-step
        genre_objects_for_book = Genre.objects.all()
        test_book.genre.set(genre_objects_for_book) # Direct assignment of many-to-many types not allowed.
        test_book.save()

        # Create 30 BookInstance objects
        number_of_book_copies = 30
        for book_copy in range(number_of_book_copies):
            return_date = timezone.localtime() + datetime.timedelta(days=book_copy%5)
            the_borrower = test_user1 if book_copy % 2 else test_user2
            status = 'm'
            BookInstance.objects.create(
                book=test_book,
                imprint='Unlikely Imprint, 2016',
                due_back=return_date,
                borrower=the_borrower,
                status=status,
            )

    def test_redirect_if_not_logged_in(self):
        response = self.client.get(reverse('my-borrowed'))
        self.assertRedirects(response, '/accounts/login/?next=/catalog/mybooks/')

    def test_logged_in_uses_correct_template(self):
        login = self.client.login(username='testuser1', password='1X&lt;ISRUkw+tuK')
        response = self.client.get(reverse('my-borrowed'))

        # Check our user is logged in
        self.assertEqual(str(response.context['user']), 'testuser1')
        # Check that we got a response "success"
        self.assertEqual(response.status_code, 200)

        # Check we used correct template
        self.assertTemplateUsed(response, 'catalog/bookinstance_list_borrowed_user.html')
</pre>

<p>To verify that the view will redirect to a login page if the user is not logged in we use <code>assertRedirects</code>, as demonstrated in <code>test_redirect_if_not_logged_in()</code>. To verify that the page is displayed for a logged in user we first log in our test user, and then access the page again and check that we get a <code>status_code</code> of 200 (success). </p>

<p>The rest of the tests verify that our view only returns books that are on loan to our current borrower. Copy the code below and paste it onto the end of the test class above.</p>

<pre class="brush: python">    def test_only_borrowed_books_in_list(self):
        login = self.client.login(username='testuser1', password='1X&lt;ISRUkw+tuK')
        response = self.client.get(reverse('my-borrowed'))

        # Check our user is logged in
        self.assertEqual(str(response.context['user']), 'testuser1')
        # Check that we got a response "success"
        self.assertEqual(response.status_code, 200)

        # Check that initially we don't have any books in list (none on loan)
        self.assertTrue('bookinstance_list' in response.context)
        self.assertEqual(len(response.context['bookinstance_list']), 0)

        # Now change all books to be on loan
        books = BookInstance.objects.all()[:10]

        for book in books:
            book.status = 'o'
            book.save()

        # Check that now we have borrowed books in the list
        response = self.client.get(reverse('my-borrowed'))
        # Check our user is logged in
        self.assertEqual(str(response.context['user']), 'testuser1')
        # Check that we got a response "success"
        self.assertEqual(response.status_code, 200)

        self.assertTrue('bookinstance_list' in response.context)

        # Confirm all books belong to testuser1 and are on loan
        for bookitem in response.context['bookinstance_list']:
            self.assertEqual(response.context['user'], bookitem.borrower)
            self.assertEqual(bookitem.status, 'o')

    def test_pages_ordered_by_due_date(self):
        # Change all books to be on loan
        for book in BookInstance.objects.all():
            book.status='o'
            book.save()

        login = self.client.login(username='testuser1', password='1X&lt;ISRUkw+tuK')
        response = self.client.get(reverse('my-borrowed'))

        # Check our user is logged in
        self.assertEqual(str(response.context['user']), 'testuser1')
        # Check that we got a response "success"
        self.assertEqual(response.status_code, 200)

        # Confirm that of the items, only 10 are displayed due to pagination.
        self.assertEqual(len(response.context['bookinstance_list']), 10)

        last_date = 0
        for book in response.context['bookinstance_list']:
            if last_date == 0:
                last_date = book.due_back
            else:
                self.assertTrue(last_date &lt;= book.due_back)
                last_date = book.due_back</pre>

<p>You could also add pagination tests, should you so wish!</p>

<h4 id="Testing_views_with_forms">Testing views with forms</h4>

<p>Testing views with forms is a little more complicated than in the cases above, because you need to test more code paths: initial display, display after data validation has failed, and display after validation has succeeded. The good news is that we use the client for testing in almost exactly the same way as we did for display-only views.</p>

<p>To demonstrate, let's write some tests for the view used to renew books (<code>renew_book_librarian()</code>):</p>

<pre class="brush: python">from catalog.forms import RenewBookForm

@permission_required('catalog.can_mark_returned')
def renew_book_librarian(request, pk):
    """View function for renewing a specific BookInstance by librarian."""
    book_instance = get_object_or_404(BookInstance, pk=pk)

    # If this is a POST request then process the Form data
    if request.method == 'POST':

        # Create a form instance and populate it with data from the request (binding):
        book_renewal_form = RenewBookForm(request.POST)

        # Check if the form is valid:
        if form.is_valid():
            # process the data in form.cleaned_data as required (here we just write it to the model due_back field)
            book_instance.due_back = form.cleaned_data['renewal_date']
            book_instance.save()

            # redirect to a new URL:
            return HttpResponseRedirect(reverse('all-borrowed'))

    # If this is a GET (or any other method) create the default form
    else:
        proposed_renewal_date = datetime.date.today() + datetime.timedelta(weeks=3)
        book_renewal_form = RenewBookForm(initial={'renewal_date': proposed_renewal_date})

    context = {
        'book_renewal_form': book_renewal_form,
        'book_instance': book_instance,
    }

    return render(request, 'catalog/book_renew_librarian.html', context)</pre>

<p>We'll need to test that the view is only available to users who have the <code>can_mark_returned</code> permission, and that users are redirected to an HTTP 404 error page if they attempt to renew a <code>BookInstance</code> that does not exist. We should check that the initial value of the form is seeded with a date three weeks in the future, and that if validation succeeds we're redirected to the "all-borrowed books" view. As part of checking the validation-fail tests we'll also check that our form is sending the appropriate error messages.</p>

<p>Add the first part of the test class (shown below) to the bottom of <strong>/catalog/tests/test_views.py</strong>.
This creates two users and two book instances, but only gives one user the permission required to access the view.</p>

<pre class="brush: python">import uuid

from django.contrib.auth.models import Permission # Required to grant the permission needed to set a book as returned.

class RenewBookInstancesViewTest(TestCase):
    def setUp(self):
        # Create a user
        test_user1 = User.objects.create_user(username='testuser1', password='1X&lt;ISRUkw+tuK')
        test_user2 = User.objects.create_user(username='testuser2', password='2HJ1vRV0Z&amp;3iD')

        test_user1.save()
        test_user2.save()

        # Give test_user2 permission to renew books.
        permission = Permission.objects.get(name='Set book as returned')
        test_user2.user_permissions.add(permission)
        test_user2.save()

        # Create a book
        test_author = Author.objects.create(first_name='John', last_name='Smith')
        test_genre = Genre.objects.create(name='Fantasy')
        test_language = Language.objects.create(name='English')
        test_book = Book.objects.create(
            title='Book Title',
            summary='My book summary',
            isbn='ABCDEFG',
            author=test_author,
            language=test_language,
        )

        # Create genre as a post-step
        genre_objects_for_book = Genre.objects.all()
        test_book.genre.set(genre_objects_for_book) # Direct assignment of many-to-many types not allowed.
        test_book.save()

        # Create a BookInstance object for test_user1
        return_date = datetime.date.today() + datetime.timedelta(days=5)
        self.test_bookinstance1 = BookInstance.objects.create(
            book=test_book,
            imprint='Unlikely Imprint, 2016',
            due_back=return_date,
            borrower=test_user1,
            status='o',
        )

        # Create a BookInstance object for test_user2
        return_date = datetime.date.today() + datetime.timedelta(days=5)
        self.test_bookinstance2 = BookInstance.objects.create(
            book=test_book,
            imprint='Unlikely Imprint, 2016',
            due_back=return_date,
            borrower=test_user2,
            status='o',
        )</pre>

<p>Add the following tests to the bottom of the test class. These check that only users with the correct permissions (<em>testuser2</em>) can access the view. We check all the cases: when the user is not logged in, when a user is logged in but does not have the correct permissions, when the user has permissions but is not the borrower (should succeed), and what happens when they try to access a <code>BookInstance</code> that doesn't exist. We also check that the correct template is used.</p>

<pre class="brush: python">   def test_redirect_if_not_logged_in(self):
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}))
        # Manually check redirect (Can't use assertRedirect, because the redirect URL is unpredictable)
        self.assertEqual(response.status_code, 302)
        self.assertTrue(response.url.startswith('/accounts/login/'))

    def test_forbidden_if_logged_in_but_not_correct_permission(self):
        login = self.client.login(username='testuser1', password='1X&lt;ISRUkw+tuK')
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}))
        self.assertEqual(response.status_code, 403)

    def test_logged_in_with_permission_borrowed_book(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&amp;3iD')
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance2.pk}))

        # Check that it lets us login - this is our book and we have the right permissions.
        self.assertEqual(response.status_code, 200)

    def test_logged_in_with_permission_another_users_borrowed_book(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&amp;3iD')
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}))

        # Check that it lets us login. We're a librarian, so we can view any users book
        self.assertEqual(response.status_code, 200)

    def test_HTTP404_for_invalid_book_if_logged_in(self):
        # unlikely UID to match our bookinstance!
        test_uid = uuid.uuid4()
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&amp;3iD')
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk':test_uid}))
        self.assertEqual(response.status_code, 404)

    def test_uses_correct_template(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&amp;3iD')
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}))
        self.assertEqual(response.status_code, 200)

        # Check we used correct template
        self.assertTemplateUsed(response, 'catalog/book_renew_librarian.html')
</pre>

<p>Add the next test method, as shown below. This checks that the initial date for the form is three weeks in the future. Note how we are able to access the value of the initial value of the form field (<code>response.context['form'].initial['renewal_date'])</code>.</p>

<pre class="brush: python">    def test_form_renewal_date_initially_has_date_three_weeks_in_future(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&amp;3iD')
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}))
        self.assertEqual(response.status_code, 200)

        date_3_weeks_in_future = datetime.date.today() + datetime.timedelta(weeks=3)
        self.assertEqual(response.context['form'].initial['renewal_date'], date_3_weeks_in_future)
</pre>

<div class="notecard warning">
  <p><strong>Warning:</strong> If you use the form class <code>RenewBookModelForm(forms.ModelForm)</code> instead of class <code>RenewBookForm(forms.Form)</code>, then the form field name is <strong>'due_back'</strong> instead of <strong>'renewal_date'</strong>.</p>
</div>

<p>The next test (add this to the class too) checks that the view redirects to a list of all borrowed books if renewal succeeds. What differs here is that for the first time we show how you can <code>POST</code> data using the client. The post <em>data</em> is the second argument to the post function, and is specified as a dictionary of key/values.</p>

<pre class="brush: python">    def test_redirects_to_all_borrowed_book_list_on_success(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&amp;3iD')
        valid_date_in_future = datetime.date.today() + datetime.timedelta(weeks=2)
        response = self.client.post(reverse('renew-book-librarian', kwargs={'pk':self.test_bookinstance1.pk,}), {'renewal_date':valid_date_in_future})
        self.assertRedirects(response, reverse('all-borrowed'))
</pre>

<div class="notecard warning">
  <p><strong>Warning:</strong> The <em>all-borrowed</em> view was added as a <em>challenge</em>, and your code may instead redirect to the home page '/'. If so, modify the last two lines of the test code to be like the code below. The <code>follow=True</code> in the request ensures that the request returns the final destination URL (hence checking <code>/catalog/</code> rather than <code>/</code>).</p>

  <pre class="brush: python"> response = self.client.post(reverse('renew-book-librarian', kwargs={'pk':self.test_bookinstance1.pk,}), {'renewal_date':valid_date_in_future}, follow=True )
 self.assertRedirects(response, '/catalog/')</pre>
</div>

<p>Copy the last two functions into the class, as seen below. These again test <code>POST</code> requests, but in this case with invalid renewal dates. We use <code>assertFormError()</code> to verify that the error messages are as expected.</p>

<pre class="brush: python">    def test_form_invalid_renewal_date_past(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&amp;3iD')
        date_in_past = datetime.date.today() - datetime.timedelta(weeks=1)
        response = self.client.post(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}), {'renewal_date': date_in_past})
        self.assertEqual(response.status_code, 200)
        self.assertFormError(response, 'form', 'renewal_date', 'Invalid date - renewal in past')

    def test_form_invalid_renewal_date_future(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&amp;3iD')
        invalid_date_in_future = datetime.date.today() + datetime.timedelta(weeks=5)
        response = self.client.post(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}), {'renewal_date': invalid_date_in_future})
        self.assertEqual(response.status_code, 200)
        self.assertFormError(response, 'form', 'renewal_date', 'Invalid date - renewal more than 4 weeks ahead')
</pre>

<p>The same sorts of techniques can be used to test the other view.</p>

<h3 id="Templates">Templates</h3>

<p>Django provides test APIs to check that the correct template is being called by your views, and to allow you to verify that the correct information is being sent. There is however no specific API support for testing in Django that your HTML output is rendered as expected.</p>

<h2 id="Other_recommended_test_tools">Other recommended test tools</h2>

<p>Django's test framework can help you write effective unit and integration tests — we've only scratched the surface of what the underlying <strong>unittest</strong> framework can do, let alone Django's additions (for example, check out how you can use <a href="https://docs.python.org/3.5/library/unittest.mock-examples.html">unittest.mock</a> to patch third party libraries so you can more thoroughly test your own code).</p>

<p>While there are numerous other test tools that you can use, we'll just highlight two:</p>

<ul>
 <li><a href="https://coverage.readthedocs.io/en/latest/">Coverage</a>: This Python tool reports on how much of your code is actually executed by your tests. It is particularly useful when you're getting started, and you are trying to work out exactly what you should test.</li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Your_own_automation_environment">Selenium</a> is a framework to automate testing in a real browser. It allows you to simulate a real user interacting with the site, and provides a great framework for system testing your site (the next step up from integration testing).</li>
</ul>

<h2 id="Challenge_yourself">Challenge yourself</h2>

<p>There are a lot more models and views we can test. As a simple task, try to create a test case for the <code>AuthorCreate</code> view.</p>

<pre class="brush: python">class AuthorCreate(PermissionRequiredMixin, CreateView):
    model = Author
    fields = '__all__'
    initial = {'date_of_death':'12/10/2016'}
    permission_required = 'catalog.can_mark_returned'</pre>

<p>Remember that you need to check anything that you specify or that is part of the design. This will include who has access, the initial date, the template used, and where the view redirects on success.</p>

<h2 id="Summary">Summary</h2>

<p>Writing test code is neither fun nor glamorous, and is consequently often left to last (or not at all) when creating a website. It is however an essential part of making sure that your code is safe to release after making changes, and cost-effective to maintain.</p>

<p>In this tutorial we've shown you how to write and run tests for your models, forms, and views. Most importantly we've provided a brief summary of what you should test, which is often the hardest thing to work out when you're getting started. There is a lot more to know, but even with what you've learned already you should be able to create effective unit tests for your websites.</p>

<p>The next and final tutorial shows how you can deploy your wonderful (and fully tested!) Django website.</p>

<h2 id="See_also">See also</h2>

<ul>
 <li><a href="https://docs.djangoproject.com/en/3.1/topics/testing/overview/">Writing and running tests</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/intro/tutorial05/">Writing your first Django app, part 5 &gt; Introducing automated testing</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/topics/testing/tools/">Testing tools reference</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/topics/testing/advanced/">Advanced testing topics</a> (Django docs)</li>
 <li><a href="https://toastdriven.com/blog/2011/apr/10/guide-to-testing-in-django/">A Guide to Testing in Django</a> (Toast Driven Blog, 2011)</li>
 <li><a href="https://test-driven-django-development.readthedocs.io/en/latest/index.html">Workshop: Test-Driven Web Development with Django</a> (San Diego Python, 2014)</li>
 <li><a href="https://realpython.com/blog/python/testing-in-django-part-1-best-practices-and-examples/">Testing in Django (Part 1) - Best Practices and Examples</a> (RealPython, 2013)</li>
</ul>

<p>{{PreviousMenuNext("Learn/Server-side/Django/Forms", "Learn/Server-side/Django/Deployment", "Learn/Server-side/Django")}}</p>

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
 <li><strong>Django Tutorial Part 10: Testing a Django web application</strong></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Deployment">Django Tutorial Part 11: Deploying Django to production</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/web_application_security">Django web application security</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/django_assessment_blog">DIY Django mini blog</a></li>
</ul>