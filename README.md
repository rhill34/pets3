# pets3
# IT 328 Pair Program: Pets III Fat-Free Templating For this pair program, you will incorporate data validation into your **pets** project. You can preview the app at [http://tostrander.greenriverdev.com/328/pets3/](http://tostrander.greenriverdev.com/328/pets3/) ## Project Setup We will begin by copying our previous project (pets2) to a new one (pets3). ### Partner 1: 1\. Create a new GitHub repo called pets3\. Do NOT add a README file. 2\. Add Partner 2 as a collaborator. 3\. Copy the URL of the new repository. 4\. In the editor, open a terminal window and clone the **pets3** repository to your 328 directory. Run a directory listing to make sure that pets3 is there. [328]$ ls dating hello hello2 pets pets2 pets3 5\. Next, copy _only_ **composer.json**,**index.php**, **.htaccess**, and **views** from pets2 to pets3\. The command for the first copy is shown here: [328]$ cp pets2/composer.json pets3 6\. In the editor, download the pets3 directory to local, and then modify composer.json so that it has **pets3** in the name, and both partners as authors. Save the file. 7\. From the pets3 directory in the terminal window, install composer. In the editor, exclude the vendor directory from your path. 8\. Add your files and views folder to git. Do _not_ add the vendor folder. [pets3]$ git add README.md composer.json .htaccess index.php views 9\. Do a git commit and then push. Check GitHub in a web browser to verify that your files are there. ### Partner 2: 1\. Accept your invitation to collaborate. 2\. Copy the URL of the pets3 repository. 3\. Clone the repository in your 328 directory. You should now have a 328/pets3 directory. 4\. Install composer, and then exclude the vendor directory from your path. 5\. Download your pets3 directory to local. Make all of your changes on local. ## Template Modification Take turns completing the steps below. After each step, add-commit-push your code to GitHub. Before beginning the next step, do a git pull and then Download to local. Remember to make all of your changes to your local files. They should automatically be saved to your remote directory. Be sure to test as you go! We will begin by modifying the form2.html template so that it populates the drop-down list using an array of colors. 1\. At the top of your index page, after instantiating Fat-free but before the routing, define an array of valid colors and assign it to a Fat-free variable. By defining this variable outside of a function, we make it available throughout the application. $f3 = Base::instance(); **$f3->set('colors', array('pink', 'green', 'blue')); ** 2\. In form2.html, you have a list of colors. Modify the form so that it uses F3's templating language to populate the drop-down list using your color array:

<pre> <select class="form-control" name="color" id="color"><option>--Select a color--</option>  

    **  

        <option>{{ @colorOption }}</option>  

    **</select> </pre>

3\. In your index page, modify the order2 route so that it displays form2 as a _Template_ instead of a _View._ Test and commit your changes. $template = new **Template**(); echo $template->render('views/form2.html'); ## Route Modification Next, we will modify our routing so that users will have an opportunity to view validation errors before going on to the next page. 1\. Ensure that error reporting is turned on in your index.php page, both for PHP and Fat-Free

<pre>**//Turn on error reporting  

error_reporting(_E_ALL_);  

ini_set('display_errors', TRUE);**  

//Require autoload  

require_once('vendor/autoload.php');  

//Create an instance of the Base class  

$f3 = Base::_instance_();  

**//Set debug level  

$f3->set('DEBUG', 3);**</pre>

2\. Modify your existing forms (form1.html and form2.html) so that each form posts to itself. That way, if there is an error, the user will have an opportunity to correct it before moving on.3\. Currently, in our index page, we have an "order" route that is accessed through the GET method. The user can get to this page by typing 328/pets3/order, or by clicking the "Order a Pet" link on the home page.

<pre>$f3->route('GET /order',  

    function() {  

        $view = new View;  

        echo $view->render('views/form1.html');  

    });</pre>

![](file:///C:/Users/rhill/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png) Navigate to the order page and click Next. What happens? Why? Talk about this with your partner. When the user first visits the "order" page, it will be through the GET method. But once they've submitted the form, the same page will be accessed through the POST method, because the form is posting to itself. We therefore need to modify our route to handle requests via _either method_ (GET or POST):

<pre>$f3->route('GET**|POST** /order',  

    **function**() {  

        $template = **new** Template();  

        **echo** $template->render('views/form1.html');  

    });</pre>

Make the same change to the order2 route. Test your app by navigating again to the **order** page and clicking the Next button. Now you should see the same page (because the form is posting to itself), and no error message. ## The Model We will now create a file containing validation functions in our **_model_**. 1\. In your project directory, create a new subdirectory called**model** which contains a file called **validation-functions.php**. 2\. Define a validColor() function that takes a color parameter, and returns**true** if the color is in an array of valid colors, and **false** otherwise. Note: you can use the array of colors you defined in the index page by giving your Fat-free object global scope!

<pre>       /* Validate a color  

 *  

 * @param String color  

 * @return boolean  

 */  

**function** validColor($color)  

{  

    **global** $f3;  

    **return** in_array($color, $f3->get('colors'));  

}</pre>

3\. Define a validString() function that takes a string parameter, and returns **true** if it is not empty, and if it is all alphabetic. We will be using this function to validate the animal type. ## Data Validation Time to bring it all together! 1\. In your index page, just before defining your routes, require your validation file. We include the model file here so that it will be available to all of our routes.

<pre>**require_once**('model/validation-functions.php');</pre>

2\. Make the following changes to your **order** route: a. Modify the anonymous function in your route to accept the $f3 parameter. b. Inside the anonymous function, clear your SESSION variable in case there is data there from a previous form submission. c. If animal is set in the POST array (i.e. the form has been submitted), then: i. Get the animal from the POST array ii. If the animal is valid then add it to a SESSION variable and reroute the user to order2 iii. If the animal is not valid then create an error variable that stores an error message in the F3 hive.

<pre>$f3->route('GET|POST /order',  

    **function****($f3**) {  

        $_SESSION = **array**();  

        **if** (**isset**($_POST['animal'])) {  

            $animal = $_POST['animal'];  

            **if** (validText($animal)) {  

                $_SESSION['animal'] = $animal;  

                $f3->reroute('/order2');  

            } **else** {  

                $f3->set("errors['animal']", "Please enter an animal.");  

            }  

        }  

        $template = **new** Template();  

        **echo** $template->render('views/form1.html');  

    });</pre>

3\. To test your changes, try submitting the order form. If you leave the animal field blank or enter a number in that field, you should stay on the page and see an error message. If you enter a text value for animal, then you should be directed to the next page. 4\. Modify your order2 route so that it gets the color and validates it. If it's valid, it should store the color in the SESSION variable and redirect to the results page. If it's not valid, store an error in the hive. 5\. Make sure your results page displays the data from the SESSION array:

<pre>
### Results page

Thank you for ordering a  

    {{ @SESSION['color'] }} {{ @SESSION['animal'] }}!

</pre>

## Modifying the Forms Next, we will modify the forms to display the errors. We will also make the forms "sticky." 1\. Above the color dropdown, display an error message if $errors[‘color’] is set

<pre>

<div class="col-sm-3">  

    ** <check if="{{ isset(@errors['color']) }}">{{ @errors['color'] }}</check> **  

    <select class="form-control" name="color">  

        ...  

    </select>  

</div>

</pre>

2\. Make the color drop-down sticky. If the option that we are currently displaying (@colorOption) is the same as the color the user selected (@color), then we display "selected" in the option tag.

<pre>![](file:///C:/Users/rhill/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png)![](file:///C:/Users/rhill/AppData/Local/Temp/msohtmlclip1/01/clip_image006.png)  

<label class="col-sm-1 control-label" for="color">Pet Color</label>  

<div class="col-sm-3">  

    <check if="{{ @errors['color'] }}">  

{{ @errors['color'] }}

    </check>  

    <select class="form-control" name="color">  

        <option>--Select--</option>  

                       <option **<check="" if="{{ @colorOption == @color }}">selected** {{ @colorOption }}  

                       </option>  

    </select>  

</div>

</pre>

3\. Make the animal text box sticky, and display an error if there is one. 4\. Test, test, test!
