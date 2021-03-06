# Unified Meta Box Order

Intuitively harmonize column layout and meta box positions across all backend users of your WordPress installation.  

Credit: This plugin is derived from the abandoned [Global Meta Box Order plugin](https://wordpress.org/plugins/global-meta-box-order) with hopes to be more stable and better support new versions of Wordpress.

## Quick Overview

* Install and activate the plugin
* Switch to a post, a page, a custom post type, or the dashboard
* Change the order and position of a meta box (or all of them)
* Change the column layout
* Switch to a different user (maybe with the help of the [_User Switching_](https://wordpress.org/plugins/user-switching/) plugin)
* See your changes applied   

## Installation

Download and unpack, then move the folder 'unified-meta-box-order' into your 'plugins' folder. Head over to your WordPress installation and activate the plugin in the admin area.

## Rollback

The plugin doesn't write anything to the database, it just reads. So it never touches any user settings, but instead filters them on a per request basis. Though all applied changes _appear_ to be permanent from a user's perspective, they are not. Just deactivate the plugin and see all changes disappear. Activate it again, and they will all be reapplied.  

## How It Works

The plugin operates on a blueprint user whose screen settings for meta boxes (visibility, position and ordering), and column layout are cloned for all other backend users on the fly.

By default, this blueprint user is the first admin user found, so you'll need to be logged in as **that user** to globally change screen settings.   

For how to change the default blueprint user, see [_Configuration_](#configuration) below.

## Where It Works 

By default, the plugin kicks in when a user:

* edits a post
* edits a page
* edits a custom post type
* hits the dashboard

See [_Configuration_](#configuration) on how to limit its scope.

## What It Changes

It will always change

* the meta boxes visibility, ordering, and column positions
* the column layout

When told so, it will also

* remove the screen options box
* immobilize all boxes

Again, please refer to the [configuration](#configuration) for details.   

## Usage

Log in as your blueprint user. By default, the is the first admin user found in your system.   

Select an editing screen (post, page, custom post type) or the dashboard, move the meta boxes around, change their screen settings and the screen's column layout. Switch to some user to review your settings, switch back to adjust them.

When finally everything is in its place, you might want to [lock your views down](#locking_views). 

## <a id="configuration"></a>Configuration

The backend integration is kept to a minimum. No navigation entry, no options page, no entry in the database. Instead, the place to go to configure the plugin is your theme's `functions.php`.

By the way: You don't _need_ to configure it. As long as it finds an admin user, it will work just fine.

### <a id="preparation"></a>Preparation

Fire up an editor, load your functions.php, and copy and paste the following code into it. The idea is to have some sort of container to do the configuration in, but do it any way you like.

For brevity, we'll assume the plugin is loaded and active, so we won't check for that (see this nice [write-up](http://queryloop.com/how-to-detect-if-a-wordpress-plugin-is-active/) on _QueryLoop_ on some ways to do it, though).

```PHP
if (is_admin()) {

    // The path to the configuation is rather long, so let's
    // make us a shorthand.
    class_alias('\GlobalMetaBoxOrder\Config', 'MetaBoxConfig');

    // Add MetaBoxConfig below this line
    ...
}
```

Now we are ready to add some of the configuration settings below to adjust the plugin's default behaviour. You might also want to have a look at the [example configuration](#example_config) near the end of this document.

Please keep in mind that you need to be logged in as any user but your blueprint user to see a setting applied. Again, the [_User Switching_](https://wordpress.org/plugins/user-switching/) plugin might come in handy.

### Screens To Operate On

By default, the plugin operates on the post, page, and custom post type editing screens, and the dashboard.

You can change this as follows:

```PHP
// Operate on post and page screens only, leave the dashboard alone.
// This will still include custom post types.
MetaBoxConfig::$filter = array('post', 'page');

// Exclude custom post types
MetaBoxConfig::$include_cpts = false; 

// Allow custom post types...
MetaBoxConfig::$include_cpts = true; 

// ...but not all of them
MetaBoxConfig::$exclude = array('acme_product');
```

`MetaBoxConfig` in the example above is assumed to be an alias to `\GlobalMetaBoxOrder\Config` as shown in the [preparation](#preparation) section.    

### Changing the Blueprint User

Register a function that returns a user id, like so:

```PHP
MetaBoxConfig::$getBlueprintUserId = function () { return 1; };
```

Or, more involved:

```PHP
MetaBoxConfig::$getBlueprintUserId = function () { 
            
    $user = get_user_by('slug', 'jane');
    return $user ? $user->ID : false; 
};
```

`MetaBoxConfig` in the examples above is assumed to be an alias to `\GlobalMetaBoxOrder\Config` as shown in the [preparation](#preparation) section.    

### <a id="locking_views"></a>Locking Views

By default, all users will be able to interact with the screen options box, and to move around the meta boxes themselves. There is a rationale behind it, but to cut things short, this is how you might want to change it:

```PHP
// No screen options 
MetaBoxConfig::$remove_screen_options = true;

// Meta boxes can't be moved anymore 
MetaBoxConfig::$lock_meta_box_order = true; 
```

`MetaBoxConfig` in the example above is assumed to be an alias to `\GlobalMetaBoxOrder\Config` as shown in the [preparation](#preparation) section.    

### <a id="example_config"></a>Example Configuration

```PHP
if (is_admin()) {

    // Make sure plugin is active
    if (class_exists('\GlobalMetaBoxOrder\Config')) {

        // Make a long name short. 
        class_alias('\GlobalMetaBoxOrder\Config', 'MetaBoxConfig');

        // Settings

        MetaBoxConfig::$filter = array('post', 'page', 'dashboard'); // default
        MetaBoxConfig::$include_cpts = true; // default
        MetaBoxConfig::$getBlueprintUserId = function () { return 1; };
        MetaBoxConfig::$exclude = array('acme_product');
        MetaBoxConfig::$remove_screen_options = true;
        MetaBoxConfig::$lock_meta_box_order = true; 
    }
}
```

## On Moving the WYSIWYG Editor

The position of WordPress' WYSIWYG editor is fixed, and can't be changed out of the box (mostly because it lacks a box around it). There are reasons for this, but if you want to have a positionable editor anyway, you might want to have a look at [_Movable Editor_](https://github.com/pontycode/wordpress-movable-editor) plugin.

If, on the other hand, if you want to place one specific box _above_ the editor, you might want to check out [this answer](http://wordpress.stackexchange.com/a/88103) on _stackexchange_.

## Support

If you have any issues with this plugin feel free to reach out to the maintainer, bdombro@gmail.com.

## License

GNU GPL v2 or later