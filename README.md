# Guide de démarrage avec CodeIgniter 4

## Installation

Assurez-vous d'avoir PHP 7.3+ installé, puis utilisez Composer :

```bash
composer create-project codeigniter4/appstarter ci4-app
cd ci4-app
php spark serve
```

## Routes

Définissez vos routes dans `app/Config/Routes.php` :

```php
$routes->get('/', 'Home::index');
$routes->get('users', 'UserController::index');
$routes->post('users', 'UserController::create');
```

## Contrôleurs

Créez un contrôleur dans `app/Controllers/UserController.php` :

```php
<?php namespace App\Controllers;

use CodeIgniter\Controller;

class UserController extends Controller
{
    public function index()
    {
        // Afficher la liste des utilisateurs
    }

    public function create()
    {
        // Créer un nouvel utilisateur
    }
}
```

## Migrations

Créez une migration avec :

```bash
php spark make:migration create_users_table
```

Éditez le fichier généré dans `app/Database/Migrations/` :

```php
public function up()
{
    $this->forge->addField([
        'id' => ['type' => 'INT', 'constraint' => 5, 'unsigned' => true, 'auto_increment' => true],
        'username' => ['type' => 'VARCHAR', 'constraint' => 100],
        'email' => ['type' => 'VARCHAR', 'constraint' => 100],
    ]);
    $this->forge->addKey('id', true);
    $this->forge->createTable('users');
}
```

Exécutez la migration :

```bash
php spark migrate
```

## Validation de données

Dans votre contrôleur :

```php
public function create()
{
    $validation = \Config\Services::validation();
    $validation->setRules([
        'username' => 'required|min_length[3]|max_length[50]',
        'email' => 'required|valid_email',
    ]);

    if (!$validation->withRequest($this->request)->run()) {
        return view('users/create', ['validation' => $validation]);
    }

    // Traitement des données valides
}
```

## CRUD

Exemple de méthodes CRUD dans le contrôleur :

```php
public function index()
{
    $model = new \App\Models\UserModel();
    $data['users'] = $model->findAll();
    return view('users/index', $data);
}

public function create()
{
    // Voir l'exemple de validation ci-dessus
}

public function update($id)
{
    $model = new \App\Models\UserModel();
    $user = $model->find($id);
    // Mise à jour de l'utilisateur
}

public function delete($id)
{
    $model = new \App\Models\UserModel();
    $model->delete($id);
    return redirect()->to('/users');
}
```

## Middleware

Créez un middleware dans `app/Filters/AuthFilter.php` :

```php
<?php namespace App\Filters;

use CodeIgniter\HTTP\RequestInterface;
use CodeIgniter\HTTP\ResponseInterface;
use CodeIgniter\Filters\FilterInterface;

class AuthFilter implements FilterInterface
{
    public function before(RequestInterface $request, $arguments = null)
    {
        if (!session()->get('logged_in')) {
            return redirect()->to('/login');
        }
    }

    public function after(RequestInterface $request, ResponseInterface $response, $arguments = null)
    {
        // Do something here
    }
}
```

Ajoutez le middleware dans `app/Config/Filters.php` :

```php
public $filters = [
    'auth' => \App\Filters\AuthFilter::class,
];
```

Appliquez le middleware dans `app/Config/Routes.php` :

```php
$routes->group('admin', ['filter' => 'auth'], function($routes) {
    $routes->get('users', 'UserController::index');
});
```

Ce guide couvre les bases pour démarrer avec CodeIgniter 4. N'hésitez pas à explorer la documentation officielle pour plus de détails sur chaque concept.
