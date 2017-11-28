[![Build Status](https://travis-ci.org/geggleto/PHP-View.svg?branch=master)](https://travis-ci.org/geggleto/PHP-View)

## PHP Renderer

This is a renderer for rendering PHP view scripts into a PSR-7 Response object. It works well with Slim Framework 3.


### Cross-site scripting (XSS) risks

Note that PHP-View has no built-in mitigation from XSS attacks. It is the developer's responsibility to use `htmlspecialchars()` or a component like [zend-escaper](https://github.com/zendframework/zend-escaper). Alternatively, consider  [Twig-View](https://github.com/slimphp/Twig-View).



## Templates
You may use `$this` inside your php templates. `$this` will be the actual PhpRenderer object will allow you to render sub-templates

## Installation

Install with [Composer](http://getcomposer.org):

    composer require slim/php-view


## Usage with Slim 3

```php
use Slim\Views\PhpRenderer;

include "vendor/autoload.php";

$app = new Slim\App();
$container = $app->getContainer();
$container['renderer'] = new PhpRenderer("./templates" ./path/to/layouts');

$app->get('/hello/{name}', function ($request, $response, $args) {
    return $this->renderer->render($response, "/hello.php", false, $args);
});

$app->run();
```

## Usage with any PSR-7 Project
```php
//Construct the View
$phpView = new PhpRenderer("./path/to/templates" ./path/to/layouts');

//Render a Template
$response = $phpView->render(new Response(), false, $yourData);
```

## Template Variables

You can now add variables to your renderer that will be available to all templates you render.

```php
// via the constructor
$templateVariables = [
    "title" => "Title"
];
$phpView = new PhpRenderer("./path/to/templates", ./path/to/layouts', $templateVariables);

// or setter
$phpView->setAttributes($templateVariables);

// or individually
$phpView->addAttribute($key, $value);
```

Data passed in via `->render()` takes precedence over attributes.
```php
$templateVariables = [
    "title" => "Title"
];
$phpView = new PhpRenderer("./path/to/templates", './path/to/layouts', '$templateVariables);

//...

$phpView->render($response, $template, false, [
    "title" => "My Title"
]);
// In the view above, the $title will be "My Title" and not "Title"
```

## Rendering in Layouts
You can now render view in another views called layouts, this allows you to compose modular view templates
and help keep your views DRY.
```php
// via the constructor
$templateVariables = [
    "title" => "Title"
];

//create your layout ./path/to/layouts/default.phtml
   <body> <?php print $content ?></body>

$phpView = new PhpRenderer("./path/to/templates", './path/to/layouts', $templateVariables);
$phpview->render($response, $template, $layout, ['data' => 'value']);

// $response will now contain "<body>value</body>" the rendered view template inside layout
```
Please note, the $content is special variable used inside layouts to render the wrapped view and should not be set
in your view paramaters.

## Disabling Layouts
If you don't want to make use of layouts, you can opt out by simply passing false to `render` method.
```php
$templateVariables = ['title' => 'Title'];

 $phpView = new PhpRenderer("./path/to/templates", ./path/to/layouts', $templateVariables);
 
 $phpview->render($response, './templates', false, ['more' => 'data'];
 // renders view without layout
 ```
## Exceptions
`\RuntimeException` - if template does not exist

`\InvalidArgumentException` - if $data contains 'template'
