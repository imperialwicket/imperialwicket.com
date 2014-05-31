{
  "title": "Adding Configuration Options to Habari Themes",
  "description": "Adding Configuration Options to Habari Themes",
  "date": "2010-10-23",
  "url": "adding-configuration-options-to-habari-themes/",
  "type": "post",
  "tags": [
    "habari"
  ]
}
When I set out to customize a basic Habari theme, the first thing I noticed when selecting a theme to begin customizing was that very few had Settings options.  After combing through a dozen or so, I was able to identify the techniques for enabling Habari theme settings, and I thought I would relay here.

I started with the K2 theme (included in the Habari package), and sought to make a basic customization.  The K2 theme is simple, clean, with straight-forward html and css; and I decided to target a settings option that controls the header color.

The first step was getting a Settings button.  To trigger the button, add the following function to your theme.php file:
<pre>
/**
*  Make the theme configurable...
*
*/	 	 	
public function filter_theme_config($configurable)
{
	$configurable = true;
	return $configurable;
}
</pre>

Pretty clear, the theme checks for $configurable, and we just need to tell the theme that is, in fact, expecting configuration.  So now we have a button, and if you load up the theme, you can press the "Settings" button, and it comes pre-fabricated with a close option.  No options yet; that's next on the list.

Adding options, we need to tell the theme to add a form to the display when someone clicks on the "Settings" button.  Add this code (I will let the excessive comments speak for themselves) to your theme.php file:

<pre>
/**
* Respond to the user selecting 'configure' on the themes page
*
*/
public function action_theme_ui()
{
    // Create a new form
    $form_header_bg_color = new FormUI( 'line_color_form' );

    // Create an instance of for code clarity, 
    //   and assign the form: 
    //     type (text), 
    //     element id (line_color, the form will become id=line_color_form),
    //     key (iw_simple__header_bg_color - used as "name" value in habari__options db table),
    //     title (Habari's text control - '_t()' - and the text to display to the user for this form.
    $header_bg_color = $form_header_bg_color->append('text', 'line_color', 'iw_simple__header_bg_color', _t( 'Header Background Color' ) );

    // Apply a template and basic settings for the display
    $header_bg_color->template = 'optionscontrol_text';
    $header_bg_color->class = 'clear';
    $header_bg_color->raw = true;

    // Set the help text
    $header_bg_color->helptext = _t( 'Enter a color in hex (ex. #d8d9d9).  
The iW simple default is #cccccc' );

    // Add controls for updatign the configuration, and responding to the user after update
    $form_header_bg_color->append('submit', 'save', 'Save');
    $form_header_bg_color->on_success( array( $this, 'save_config' ) );
    $form_header_bg_color->out();
}

// The 'save_config' function referenced above.  Create this 
//   function separately so that multiple options can use it.
public function save_config( $form ){
    Session::notice( _t( 'Theme Settings Updated' ) );
    $form->save();
    return true;
}
</pre>

Now you can interact with a text entry form and save an option to your database.  The option will save with the name provided above ("iw_simple__header_bg_color", in my example) in the  [db_prefix]options table.  One last step, tell your them to use the value.  At this step, I'll admit that I think there is probably a better way to do this, but this works, and it will get you started with adding Settings to your theme.  If anyone has cleaner implementations for the usage of those settings, feel free to add details in the comments.  

First I created function in theme.php to fetch the option, and echo the css to implement it (If you are using the K2 theme as a starting point, also comment the css directive in the current stylesheet to keep things cleaner.).  I did this:
<pre>
public function get_css_config(){
    // Some tags
    $script_begin = '<style type="text/css">';
    $script_end = '</style>';								
    $css = "";

    // Get the option and build the string.  
    //   This is an ugly implementation, but gets the job done.
    $color = Options::get( 'iw_simple__header_bg_color' );
    if($color != null){
        $css = $script_begin.'#header{background: '.$color.';}'.$script_end;
    }else{
        $css = $script_begin.'#header{background: #cccccc;}'.$script_end;
    }

    echo $css;
}
</pre>

For the K2 theme, I then updated the header.php file.  Simply adding a reference to the get_css_config() function within the head tags does the trick (remove the space at the beginning php tag):
<pre>
    < ?php $theme->get_css_config(); ?>
</pre>

Now I can change the header color from the themes section, and I no longer need to manually edit the template files themselves.  This particular update is not earth-shattering in terms of configuration options, but now I can start creating a more meaningful template that has a few built in color schemes, or multiple positioning options.

Apologies for the code formatting - I am working on a new theme...

<span class="signature">
// TODO Habari Theme(s), How to win a croquet match, GWT and App Engine
// -- imperialWicket
</span>
