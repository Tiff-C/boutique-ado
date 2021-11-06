# Boutique Ado - Walkthrough Project

## Getting started

1. Download Django to workspace using:
    `pip3 install django`
    - To check if this has been installed correctly you can use the below code in the cli
        ```
        cd /workspace/.pip-modules/lib/python3.<VERSION_NUMBER>/site-packages
        ```
        To check the python version you are using type `python3` into the CLI you will see the version number listed there.
    - Type `ls -la` in the CLI and it will list all the packages currently installed in the workspace.
    - Use `cd -` to get back to the last directory you were working in.


2. Create the project in the current workspace
    `django-admin startproject <PROJECT_NAME> .`  
    The '.' at the end creates the project in the current directory

    - Run the Project locally to check it has been created correctly using:
        `python3 manage.py runserver`

4. Run the first migrations to set up the app using:
    `python manage.py migrate`

5. Create a superuser using:
    `python3 manage.py createsuperuser`

### Setting up user authentication using Django-AllAuth

#### Install AllAuth

1. Install Django-AllAuth use `pip3 install django-allauth`.  
    For this project to ensure the version of allauth installed is the same as the one used in the walkthrough project videos I will be using `pip3 install django-allauth==0.41.0`.
2. Follow the [allauth documentation](https://django-allauth.readthedocs.io/en/latest/installation.html) to add the required settings to `settings.py`.
3. In `settings.py` add the following code:
    ```python
    EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

    ACCOUNT_AUTHENTICATION_METHOD = 'username_email'
    ACCOUNT_EMAIL_REQUIRED = True
    ACCOUNT_EMAIL_VERIFICATION = 'mandatory'
    ACCOUNT_SIGNUP_EMAIL_ENTER_TWICE = True
    ACCOUNT_USERNAME_MIN_LENGTH = 4
    LOGIN_URL = '/accounts/login/'
    LOGIN_REDIRECT_URL = '/'
    ```
    The EMAIL_BACKEND variable is used for testing the emails have been setup correctly as these won't actually send until the project has been deployed. 
4. To test allauth has been setup correctly run the project and navigate to `/accounts/login` and login using the superuser account previously created during the intial project setup. Upon login it should come up with a message stating you need to verify your email. This is because we set `ACCOUNT_EMAIL_VERIFICATION = 'mandatory'` in the previous step.
5. To verify the email for the superuser manually as the project is not yet deployed use the following:
    - Login via the admin page
    - Select 'email adresses' listed under 'accounts'
    - Select 'add email address'
    - Type the username of the superuser account and search
    - Type in the email address you want to use for the account
    - Select the 'primary' and 'verified' checkboxes and save
    - Logout of the admin account and return to `/accounts/login`
    - This time when you login you should get a 404 Error for page not found. This is becasue we haven't created the view for the home page yet and we set `LOGIN_REDIRECT_URL = '/'` in the `settings.py` file
    - If you get this error message you know allauth has been setup correctly
6. Once you have confirmed allauth is working correctly use `pip3 freeze > requirements.txt` to create the `requirements.txt` file.

#### Customising the allauth templates

To customise the allauth templates you first need to copy the default allauth templates from the site packages folder this can be done using the following command in the CLI:
```
cp -r ../.pip-modules/lib/python3.<VERSION_NUMBER>/site-packages/allauth/templates/* ./templates/allauth/
```

For this project we won't be customising the `openid` and `tests` templates so these folders can be deleted.

### Create home app

- Create the app using:
    `python3 manage.py startapp home`

### Creating a model

1. Create class in `models.py` 
    ``` python
    class Item(models.Model):
    name = models.CharField(max_length=50, null=False, blank=False)
    done = models.BooleanField(null=False, blank=False, default=False)
    ```

2. Make migrations
    ``` 
    python3 manage.py makemigrations
    ```
    To see what migrations will be made before running the command you can add `--dry-run` at the end.

3. Run migration
    ```
    python manage.py migrate
    ```
    to check what migrations will be made before running the command add `--plan` at the end.


### Adding Contexts

In this project we want the bag context to be available to all templates of the app. To do this we will be creating a *contexts.py* doc that will contain the `bag_contents()` fuction. This function will take the `request` parameter from django in the same way as the views for other apps; such as *products* or *home*. Instead of rendering a template however `bag_contents` will handle the free delivery logic and return a dictionary with the below format. 

```python
    context = {
        'bag_items': bag_items,
        'total': total,
        'product_count': product_count,
        'delivery': delivery,
        'free_delivery_delta': free_delivery_delta,
        'free_delivery_threshold': settings.FREE_DELIVERY_THRESHOLD,
        'grand_total': grand_total,
    }
```

In order for this context to be available to all views across the app it will need to be added to the project level `settings.py` file in the `templates` variable under *`context_processors`*. For this project that would be adding the following code:
```python
'bag.contexts.bag_contents',
```

### Using the Django Shell

When updating the products model to include `has_sizes` we set the default to `False`, to update items already loaded into the DB from the `fixtures.json` file you can use the Django shell command in the terminal. 
```
python3 manage.py shell
```
This creates a shell that is setup using all your application settings from the project level `settings.py` document. You can then write python code and can import any project functionality into the shell in the same way you would with a view.

As the code used in the shell isn't saved anywhere once the shell is closed it is a great way to make changes to and explore the projects DB. 

I have provided and example of the code used in the shell to update the `has_sizes` property for all clothing items below:
```Python
gitpod /workspace/boutique-ado $ python3 manage.py shell
Python 3.8.12 (default, Sep 13 2021, 03:12:02) 
Type 'copyright', 'credits' or 'license' for more information
IPython 7.27.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]: from products.models import Product

In [2]: kdbb = ['kitchen_dining', 'bed_bath']

In [3]: clothes = Product.objects.exclude(category__name__in=kdbb)

In [4]: clothes.count()
Out[4]: 130

In [5]: for item in clothes:
   ...:     item.has_sizes = True
   ...:     item.save()
   ...: 

In [6]: Product.objects.filter(has_sizes=True)
Out[6]: <QuerySet [<Product: Arizona Original Bootcut Jeans>, <Product: Liz Claiborne Audra Classic Fit Straight Leg Pants>, <Product: The Foundry Supply Co. Solid Pocket TeeBig & Tall>, <Product: Liz Claiborne Classic Sophie Secretly Slender Trouser Leg Pants - Plus>, <Product: Chef Designs Black-Trimmed Cook ShirtBig & Tall>, <Product: Nike 3-pk. Performance Crew Socks - Boys>, <Product: Champion Jersey Tee>, <Product: Stafford 4-pk. Blended Cotton A-Shirts>, <Product: Stafford 4-pk. Heavyweight Crewneck T-ShirtsBig & Tall>, <Product: Levi's 529 Curvy Bootcut Jeans>, <Product: Worthington Essential Short-Sleeve Tee - Plus>, <Product: Silver Superman Shield Cufflinks>, <Product: Dickies Heavyweight Long-Sleeve Pocket Tee>, <Product: Dickies Heavyweight Fleece Full Zip HoodieBig & Tall>, <Product: Hanes 3pk. Cotton Crewneck TShirts  Big & Tall>, <Product: Armitron Now Womens Crystal-Accent White Leather Strap Watch>, <Product: Nike NA Swoosh Dri-FIT Cotton Tee>, <Product: Stafford Year-Round Pleated PantsBig & Tall>, <Product: Arizona Super-Skinny Jeans>, <Product: Nike 3-pk. Dri-FIT Fly Rise Crew Socks>, '...(remaining elements truncated)...']>

In [7]: Product.objects.filter(has_sizes=True).count()
Out[7]: 130

In [8]: exit()
```