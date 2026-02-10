
This section will walk you through creating your first eQual application with the Workbench. The app can be found at [http://equal.local/workbench/#/](http://equal.local/workbench/#/) (this may change if you modified the docker-compose.yml).

!!! note "The Worbench"
    Is a tool intended to help you build your apps visually, without worrying about the code (as much as possible).

### Creating your first component

The first step to your application are packages. Note that you will have to respect the naming convention of each component to be able to create them.

Let's start by creating a package called `tutorial` and the Model `tutorial\Post` using the component creator :

<img src="/_assets/img/workbench_creator_package.png" style="width : 49%;">
<img src="/_assets/img/workbench_creator_model.png" style="width : 49%;">

### Using the base menu

<!---TODO update link-->
By using the [base menu](#/docs/low-code/workbench/getting-started.md/#using-the-base-menu-to-find-precise-components) you can easily find the components you need. Let's look for our newly created Model :
[base menu](/docs/application-developpers/howtos-references/tutorials-examples/tutorial-building-simple-app/#)

<img src="/_assets/img/workbench_find_example.png" style="width : 30%">

### Editing your components

Let's add properties to our `Post` Model :

<img src="/_assets/img/workbench_field_title.png" style="width : 49%">
<img src="/_assets/img/workbench_field_content.png" style="width : 49%">
<img src="/_assets/img/workbench_field_published.png" style="width : 49%">
<img src="/_assets/img/workbench_field_author_name.png" style="width : 49%">

Once you have these fields you can save the model.

<img src="/_assets/img/workbench_field_list.png" style="height : 400px;">

You will need to add the function for the `computed` in the php (you can find it at /packages/tutorial/classes/Post.class.php) file to make it work, Workbench does not allow logic edition for now.

```php
<?php

public static function calcAuthorName($self){
    $result = [];
    $posts = $self->read(['id', 'creator' => ['fullname']]);
    foreach($posts as $id => $post) {
        $result[$id] = $post['creator']['fullname'];
    };
    return $result;
}
```

### Translations

Let's translate our Model `Post` in english.

<img src="/_assets/img/workbench_translation_tuto.png">

### Creating Workflows

Let's create a workflow for Post. Set the name and icons as follows

<img src="/_assets/img/workflow_tuto.png">

### Creating Views

Let's create two views for Post using the view submenu : `form.default` and `list.default` (the basic view of a model). By clicking on the [component creator](#creating-equal-components) you can notice that the context is autofilled into the fields

<img src="/_assets/img/workbench_view_creator.png">

You should end up with :

<img src="/_assets/img/workbench_view_tuto.png">

### Editing Views

Edit the views of post like that :

##### `form.default` :

<img src="/_assets/img/workbench_view_editor_tuto_form.png">

Items have a width of 25%


#### `list.default` :

<img src="/_assets/img/workbench_view_editor_tuto_list.png">

### Creating menus

Start by filtering the elements by menus. Then create a menu of type `left` and of name app in the package `tutorial`

<img src="/_assets/img/workbench_menu_create.png">

Then, open the menu editor and edit the menu like so :

<img src="/_assets/img/workbench_menu_tuto.png">

### Adding data

Navigate to the package tutorial and create some posts for our blog by accessing the initial data button:

<img src="/_assets/img/workbench_init_tuto.png">

### Initialize a package

First let's create the application for our package tutorial. Edit the `manifest.json` of the package as so :

```json
{
    "name": "tutorial",
    "description": "workbench tutorial package",
    "version": "1.0",
    "author": "YesBabylon",
    "license": "LGPL-3",
    "depends_on": [ "core" ],
    "apps": [
        {
          "id": "blog",
          "name": "Blog",
          "extends": "app",
          "description": "blog",
          "icon": "ad_units",
          "color": "#3498DB",
          "access": {
            "groups": [
              "users",
              "admins"
            ]
          },
          "params": {
            "menus": {
              "left": "app.left"
            }
          }
        }
      ],
    "tags": [ ]
}
```

<!---TODO update link-->
Then, let's use the initialize button of the <a href="/docs/low-code/workbench/getting-started.md/#package-side-menu">package side menu</a>.

<img src="/_assets/img/workbench_package_init.png">

1. These checkboxes allow you to ask eQual to initialize the package's (and choose if you want to import their initial data) dependencies before initializing it.
2. This checkbox allow you to choose if you want to import the initial data of the package.

You can initialize the package `tutorial` as so.

<!---TODO update link-->
if all gone well you see this message in your <a href="/docs/low-code/workbench/getting-started.md/#package-side-menu">package side menu</a>

<img src="/_assets/img/workbench_package_initialized.png">

now, if you return to the /apps of your instance, you should see the blog app appear :

<img src="/_assets/img/workbench_apps_icons.png">

Congratulations ! you have created your first app using eQual Workbench.
