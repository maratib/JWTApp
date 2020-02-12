# IDE Helper for Laravel 6

Laravel IDE Helper, generates correct PHPDocs for all Facade classes, to improve auto-completion.

This package generates a file at the root that your IDE understands, so it can provide accurate autocompletion. Generation is done based on the files in your project, so they are always up-to-date. 

For Laravel 6 and above follow the step given bellow.

```bash
composer require --dev barryvdh/laravel-ide-helper

# Now following are the commands you can use to generate ide-helpers

# php artisan ide-helper:generate - phpDoc generation for Laravel Facades
# php artisan ide-helper:models - phpDocs for models
# php artisan ide-helper:meta - PhpStorm Meta file
```

Automatic phpDoc generation for Laravel Facades
You can now re-generate the docs yourself (for future updates)

php artisan ide-helper:generate
Note: bootstrap/compiled.php has to be cleared first, so run php artisan clear-compiled before generating.

You can configure your composer.json to do this after each commit:

```javascript
"scripts":{
    "post-update-cmd": [
        "Illuminate\\Foundation\\ComposerScripts::postUpdate",
        "@php artisan ide-helper:generate",
        "@php artisan ide-helper:meta"
    ]
},
```
