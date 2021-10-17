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