# Boutique Ado

## Getting started

1. Download Django to workspace
    ```
    pip3 install django
    ```
    - To check if this has been installed correctly you can use the below code in the cli
        ```
        cd /workspace/.pip-modules/lib/python3.<VERSION_NUMBER>/site-packages
        ```
        To check the python version you are using type `python3` into the CLI you will see the version number listed there.
    - Type `ls -la` in the CLI and it will list all the packages currently installed in the workspace.
    - Use `cd -` to get back to the last directory you were working in.


2. Create the project in the current workspace
    ```
    django-admin startproject <PROJECT_NAME> .
    ```
    The '.' at the end creates the project in the current directory

    - Run the Project locally to check it has been created correctly
        ```
        python3 manage.py runserver
        ```

3. Create the app
    ```
    python3 manage.py startapp <PROJECT_NAME>
    ```

4. Run the first migrations to set up the app
    ```
    python manage.py migrate
    ```

5. Create a superuser
    ```
    python3 manage.py createsuperuser
    ```

## Creating a model

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