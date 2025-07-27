# Webasyst Framework API Documentation

## Table of Contents
1. [Overview](#overview)
2. [Core Framework Classes](#core-framework-classes)
3. [Controller Classes](#controller-classes)
4. [Model Classes](#model-classes)
5. [View and Template System](#view-and-template-system)
6. [Plugin System](#plugin-system)
7. [Theme System](#theme-system)
8. [Event System](#event-system)
9. [Authentication & Authorization](#authentication--authorization)
10. [Database Layer](#database-layer)
11. [Utility Classes](#utility-classes)
12. [REST API](#rest-api)
13. [Site App Specific APIs](#site-app-specific-apis)
14. [Examples & Best Practices](#examples--best-practices)

---

## Overview

Webasyst is an open-source PHP framework for developing multi-user web applications with password-protected backends and publicly available frontends. The framework follows MVC architecture patterns and provides extensive APIs for building scalable web applications.

### System Requirements
- **Web Server**: Apache, nginx, or IIS
- **PHP**: 7.4+
- **PHP Extensions**: spl, mbstring, iconv, json, gd or ImageMagick
- **Database**: MySQL 4.1+

### Framework Structure
```
wa-apps/          # Application modules
wa-config/        # Configuration files
wa-content/       # User content and uploads
wa-installer/     # Installation scripts
wa-plugins/       # Plugin modules
wa-system/        # Core framework files
wa-widgets/       # Dashboard widgets
```

---

## Core Framework Classes

### waSystem

The main system class that provides access to framework functionality.

#### Methods

##### `waSystem::getInstance()`
Returns the singleton instance of the system.

**Syntax:**
```php
public static function getInstance()
```

**Returns:** `waSystem` - The system instance

**Example:**
```php
$system = waSystem::getInstance();
```

##### `waSystem::getApp()`
Gets the current application ID.

**Syntax:**
```php
public function getApp()
```

**Returns:** `string` - Current application ID

**Example:**
```php
$app_id = wa()->getApp();
echo $app_id; // 'site', 'shop', 'blog', etc.
```

##### `waSystem::getUser()`
Gets the current user object.

**Syntax:**
```php
public function getUser()
```

**Returns:** `waUser` - Current user instance

**Example:**
```php
$user = wa()->getUser();
if ($user->isAuth()) {
    echo "User ID: " . $user->getId();
}
```

##### `waSystem::getStorage()`
Gets the storage handler for the current domain.

**Syntax:**
```php
public function getStorage()
```

**Returns:** `waStorage` - Storage instance

**Example:**
```php
$storage = wa()->getStorage();
$storage->set('key', 'value');
$value = $storage->get('key');
```

### waConfig

Configuration management class.

#### Methods

##### `waConfig::get()`
Gets a configuration value.

**Syntax:**
```php
public function get($name, $default = null)
```

**Parameters:**
- `$name` (string): Configuration key
- `$default` (mixed): Default value if key not found

**Returns:** `mixed` - Configuration value

**Example:**
```php
$config = wa()->getConfig();
$debug = $config->get('debug', false);
$db_settings = $config->get('db');
```

### waRequest

HTTP request handling class.

#### Methods

##### `waRequest::get()`
Gets GET parameter value.

**Syntax:**
```php
public function get($name = null, $default = null, $type = null)
```

**Parameters:**
- `$name` (string): Parameter name
- `$default` (mixed): Default value
- `$type` (string): Type casting ('int', 'string', 'array')

**Returns:** `mixed` - Parameter value

**Example:**
```php
$request = waRequest::getInstance();
$page = $request->get('page', 1, 'int');
$search = $request->get('q', '', 'string');
```

##### `waRequest::post()`
Gets POST parameter value.

**Syntax:**
```php
public function post($name = null, $default = null, $type = null)
```

**Example:**
```php
$data = $request->post('data', array(), 'array');
$email = $request->post('email', '', 'string');
```

##### `waRequest::request()`
Gets REQUEST parameter value (GET or POST).

**Syntax:**
```php
public function request($name = null, $default = null, $type = null)
```

### waResponse

HTTP response handling class.

#### Methods

##### `waResponse::redirect()`
Performs HTTP redirect.

**Syntax:**
```php
public function redirect($url, $code = 302)
```

**Parameters:**
- `$url` (string): Redirect URL
- `$code` (int): HTTP status code

**Example:**
```php
$response = waResponse::getInstance();
$response->redirect('/success/');
$response->redirect('https://example.com', 301);
```

##### `waResponse::json()`
Sends JSON response.

**Syntax:**
```php
public function json($data, $code = 200)
```

**Parameters:**
- `$data` (mixed): Data to encode as JSON
- `$code` (int): HTTP status code

**Example:**
```php
$response->json(array(
    'status' => 'ok',
    'data' => $result
));
```

---

## Controller Classes

### waController

Base controller class for all application controllers.

#### Methods

##### `waController::getUser()`
Gets the current user.

**Syntax:**
```php
protected function getUser()
```

**Returns:** `waUser` - Current user instance

##### `waController::getRequest()`
Gets the request object.

**Syntax:**
```php
protected function getRequest()
```

**Returns:** `waRequest` - Request instance

##### `waController::getResponse()`
Gets the response object.

**Syntax:**
```php
protected function getResponse()
```

**Returns:** `waResponse` - Response instance

**Example Controller:**
```php
class myappFrontendController extends waController
{
    public function execute()
    {
        $user = $this->getUser();
        $request = $this->getRequest();
        
        $data = array(
            'user_id' => $user->getId(),
            'search' => $request->get('q', '')
        );
        
        $this->view->assign($data);
    }
}
```

### waViewController

Controller for rendering views with templates.

#### Properties

##### `$view`
Template engine instance.

**Type:** `waSmarty`

**Example:**
```php
class myappPageController extends waViewController
{
    public function execute()
    {
        $this->view->assign('title', 'Page Title');
        $this->view->assign('content', 'Page content');
    }
}
```

### waJsonController

Controller for JSON API responses.

#### Methods

##### `waJsonController::response()`
Sends JSON response.

**Syntax:**
```php
protected function response($data = true, $code = 200)
```

**Example:**
```php
class myappApiController extends waJsonController
{
    public function execute()
    {
        try {
            $data = $this->processRequest();
            $this->response($data);
        } catch (Exception $e) {
            $this->errors = $e->getMessage();
        }
    }
}
```

### waLongActionController

Controller for long-running operations with progress tracking.

#### Methods

##### `waLongActionController::complete()`
Marks the operation as complete.

**Syntax:**
```php
protected function complete($result = true)
```

##### `waLongActionController::setProgress()`
Updates operation progress.

**Syntax:**
```php
protected function setProgress($progress, $stage = null)
```

**Parameters:**
- `$progress` (float): Progress percentage (0-1)
- `$stage` (string): Current stage description

**Example:**
```php
class myappImportController extends waLongActionController
{
    public function execute()
    {
        $total = 1000;
        for ($i = 0; $i < $total; $i++) {
            // Process item
            $this->processItem($i);
            
            // Update progress
            $progress = ($i + 1) / $total;
            $this->setProgress($progress, "Processing item " . ($i + 1));
            
            if ($this->getRequest()->post('cleanup')) {
                break;
            }
        }
        
        $this->complete('Import completed');
    }
}
```

---

## Model Classes

### waModel

Base model class for database operations.

#### Methods

##### `waModel::__construct()`
Constructor.

**Syntax:**
```php
public function __construct($table = null, $id = null)
```

**Parameters:**
- `$table` (string): Table name
- `$id` (string): Primary key field name

##### `waModel::select()`
Executes SELECT query.

**Syntax:**
```php
public function select($fields = '*')
```

**Parameters:**
- `$fields` (string): Fields to select

**Returns:** `waDbResultSelect` - Query result

**Example:**
```php
class myappProductModel extends waModel
{
    protected $table = 'myapp_product';
    
    public function getActive()
    {
        return $this->select('*')->where('status = ?', 'active')->fetchAll();
    }
    
    public function getById($id)
    {
        return $this->getById($id);
    }
}
```

##### `waModel::insert()`
Inserts new record.

**Syntax:**
```php
public function insert($data, $type = 0)
```

**Parameters:**
- `$data` (array): Data to insert
- `$type` (int): Insert type (0 = INSERT, 1 = REPLACE, 2 = IGNORE)

**Returns:** `mixed` - Insert ID or false

**Example:**
```php
$model = new myappProductModel();
$id = $model->insert(array(
    'name' => 'Product Name',
    'price' => 99.99,
    'status' => 'active'
));
```

##### `waModel::updateById()`
Updates record by ID.

**Syntax:**
```php
public function updateById($id, $data)
```

**Parameters:**
- `$id` (mixed): Record ID
- `$data` (array): Data to update

**Returns:** `int` - Number of affected rows

**Example:**
```php
$model->updateById(123, array(
    'name' => 'Updated Name',
    'price' => 149.99
));
```

##### `waModel::deleteById()`
Deletes record by ID.

**Syntax:**
```php
public function deleteById($id)
```

**Parameters:**
- `$id` (mixed): Record ID

**Returns:** `int` - Number of affected rows

### waContactModel

Model for managing contacts/users.

#### Methods

##### `waContactModel::getById()`
Gets contact by ID.

**Syntax:**
```php
public function getById($id)
```

**Returns:** `array` - Contact data

##### `waContactModel::search()`
Searches contacts.

**Syntax:**
```php
public function search($query, $options = array())
```

**Parameters:**
- `$query` (string): Search query
- `$options` (array): Search options

**Example:**
```php
$contact_model = new waContactModel();
$contacts = $contact_model->search('john', array(
    'limit' => 10,
    'offset' => 0
));
```

---

## View and Template System

### waSmarty

Smarty template engine wrapper.

#### Methods

##### `waSmarty::assign()`
Assigns variable to template.

**Syntax:**
```php
public function assign($var, $value = null)
```

**Parameters:**
- `$var` (string|array): Variable name or array of variables
- `$value` (mixed): Variable value

**Example:**
```php
$this->view->assign('title', 'Page Title');
$this->view->assign(array(
    'products' => $products,
    'categories' => $categories
));
```

##### `waSmarty::fetch()`
Renders template and returns output.

**Syntax:**
```php
public function fetch($template)
```

**Parameters:**
- `$template` (string): Template path

**Returns:** `string` - Rendered output

**Example:**
```php
$html = $this->view->fetch('product.html');
```

#### Template Functions

##### `{wa_url}`
Generates application URL.

**Syntax:**
```smarty
{wa_url action="action" param1="value1"}
```

**Example:**
```smarty
<a href="{wa_url action='edit' id=$product.id}">Edit</a>
```

##### `{$wa->user()}`
Gets current user object.

**Example:**
```smarty
{if $wa->user()->isAuth()}
    Welcome, {$wa->user()->getName()}!
{/if}
```

##### `{$wa->app()}`
Gets current application ID.

**Example:**
```smarty
{if $wa->app() == 'shop'}
    This is Shop-Script
{/if}
```

---

## Plugin System

### waPlugin

Base plugin class.

#### Methods

##### `waPlugin::getSettings()`
Gets plugin settings.

**Syntax:**
```php
public function getSettings($name = null)
```

**Parameters:**
- `$name` (string): Setting name (optional)

**Returns:** `mixed` - Setting value or all settings

##### `waPlugin::saveSettings()`
Saves plugin settings.

**Syntax:**
```php
public function saveSettings($settings)
```

**Parameters:**
- `$settings` (array): Settings to save

#### Plugin Structure

```php
class myappMypluginPlugin extends waPlugin
{
    public function init()
    {
        // Plugin initialization
    }
    
    public function getSettings($name = null)
    {
        return parent::getSettings($name);
    }
    
    public function backendMenu()
    {
        // Add backend menu items
        return array(
            array(
                'name' => 'My Plugin',
                'url' => '?plugin=myplugin'
            )
        );
    }
}
```

#### Plugin Configuration (plugin.php)

```php
return array(
    'name' => 'My Plugin',
    'description' => 'Plugin description',
    'version' => '1.0.0',
    'vendor' => 'Developer Name',
    'frontend' => true,
    'handlers' => array(
        'backend_menu' => 'backendMenu',
        'order_action.complete' => 'orderComplete'
    )
);
```

---

## Theme System

### Theme Structure

```
themes/
  mytheme/
    theme.xml          # Theme configuration
    index.html         # Main template
    page.html          # Page template
    css/              # Stylesheets
    js/               # JavaScript files
    img/              # Images
```

### theme.xml Configuration

```xml
<?xml version="1.0" encoding="utf-8"?>
<theme id="mytheme" app="site">
    <name>My Theme</name>
    <description>Theme description</description>
    <version>1.0.0</version>
    <vendor>Developer Name</vendor>
    
    <files>
        <file path="css/style.css">
            <description>Main stylesheet</description>
        </file>
    </files>
    
    <settings>
        <setting var="color" title="Primary Color" type="color" value="#007cba"/>
        <setting var="logo" title="Logo" type="file" value=""/>
    </settings>
</theme>
```

### Theme Template Functions

#### `{$wa_theme_url}`
Gets theme URL.

**Example:**
```smarty
<link rel="stylesheet" href="{$wa_theme_url}css/style.css">
```

#### `{$wa_theme_path}`
Gets theme file system path.

#### Theme Settings Access

```smarty
<style>
    body {
        background-color: {$wa_theme_settings.color};
    }
</style>
```

---

## Event System

### waEvent

Event handling system.

#### Methods

##### `waEvent::trigger()`
Triggers an event.

**Syntax:**
```php
public static function trigger($event, &$params = null)
```

**Parameters:**
- `$event` (string): Event name
- `$params` (mixed): Event parameters (passed by reference)

**Returns:** `array` - Results from event handlers

**Example:**
```php
$params = array('order_id' => 123);
$results = wa()->event('order.complete', $params);
```

#### Event Handler Registration

In plugin configuration:
```php
'handlers' => array(
    'order.complete' => 'orderComplete',
    'product.save' => 'productSave'
)
```

Plugin handler method:
```php
public function orderComplete($params)
{
    $order_id = $params['order_id'];
    // Handle order completion
    return array('processed' => true);
}
```

---

## Authentication & Authorization

### waUser

User management and authentication.

#### Methods

##### `waUser::isAuth()`
Checks if user is authenticated.

**Syntax:**
```php
public function isAuth()
```

**Returns:** `bool` - True if authenticated

##### `waUser::getId()`
Gets user ID.

**Syntax:**
```php
public function getId()
```

**Returns:** `int` - User ID or null

##### `waUser::getName()`
Gets user display name.

**Syntax:**
```php
public function getName()
```

**Returns:** `string` - User name

##### `waUser::getEmail()`
Gets user email.

**Syntax:**
```php
public function getEmail()
```

**Returns:** `string` - User email

##### `waUser::getRights()`
Gets user rights for application.

**Syntax:**
```php
public function getRights($app_id, $name = null)
```

**Parameters:**
- `$app_id` (string): Application ID
- `$name` (string): Specific right name

**Returns:** `mixed` - Rights data

**Example:**
```php
$user = wa()->getUser();

// Check authentication
if (!$user->isAuth()) {
    throw new waRightsException('Authentication required');
}

// Check specific rights
if (!$user->getRights('myapp', 'edit')) {
    throw new waRightsException('Access denied');
}

// Get user info
$user_data = array(
    'id' => $user->getId(),
    'name' => $user->getName(),
    'email' => $user->getEmail()
);
```

### waAuth

Authentication helper.

#### Methods

##### `waAuth::auth()`
Authenticates user.

**Syntax:**
```php
public static function auth($contact_id, $remember = false)
```

**Parameters:**
- `$contact_id` (int): Contact ID to authenticate
- `$remember` (bool): Whether to remember user

##### `waAuth::logout()`
Logs out current user.

**Syntax:**
```php
public static function logout()
```

---

## Database Layer

### waDbAdapter

Database adapter for executing queries.

#### Methods

##### `waDbAdapter::query()`
Executes SQL query.

**Syntax:**
```php
public function query($sql, $params = null)
```

**Parameters:**
- `$sql` (string): SQL query
- `$params` (array): Query parameters

**Returns:** `resource` - Query result

##### `waDbAdapter::select()`
Executes SELECT query and returns all rows.

**Syntax:**
```php
public function select($sql, $params = null)
```

**Returns:** `array` - Result rows

**Example:**
```php
$db = wa()->getModel()->getAdapter();

// Simple select
$products = $db->select("SELECT * FROM products WHERE status = ?", array('active'));

// Using model
$model = new waModel('products');
$products = $model->select('*')->where('status = ?', 'active')->fetchAll();
```

---

## Utility Classes

### waUtils

General utility functions.

#### Methods

##### `waUtils::datetime()`
Formats datetime.

**Syntax:**
```php
public static function datetime($timestamp, $format = null)
```

**Parameters:**
- `$timestamp` (int): Unix timestamp
- `$format` (string): Date format

**Returns:** `string` - Formatted date

##### `waUtils::excerpt()`
Creates text excerpt.

**Syntax:**
```php
public static function excerpt($text, $length = 100)
```

**Parameters:**
- `$text` (string): Source text
- `$length` (int): Maximum length

**Returns:** `string` - Text excerpt

**Example:**
```php
$excerpt = waUtils::excerpt($long_text, 200);
$date = waUtils::datetime(time(), 'Y-m-d H:i:s');
```

### waFiles

File system utilities.

#### Methods

##### `waFiles::create()`
Creates directory.

**Syntax:**
```php
public static function create($path)
```

##### `waFiles::delete()`
Deletes file or directory.

**Syntax:**
```php
public static function delete($path)
```

##### `waFiles::copy()`
Copies file or directory.

**Syntax:**
```php
public static function copy($source, $target)
```

**Example:**
```php
// Create directory
waFiles::create(wa()->getDataPath('uploads'));

// Copy file
waFiles::copy($source_file, $target_file);

// Delete file
waFiles::delete($old_file);
```

---

## REST API

### API Endpoints

Webasyst provides RESTful API endpoints for accessing application data.

#### Authentication

API uses token-based authentication:
```
GET /api.php/app/method?access_token=TOKEN
POST /api.php/app/method
Authorization: Bearer TOKEN
```

#### Common Endpoints

##### GET /api.php/contacts/
Gets contacts list.

**Parameters:**
- `limit` (int): Number of records
- `offset` (int): Records offset
- `fields` (string): Comma-separated field list

**Response:**
```json
{
    "data": [
        {
            "id": 1,
            "name": "John Doe",
            "email": "john@example.com"
        }
    ],
    "total": 100
}
```

##### GET /api.php/contacts/{id}/
Gets contact by ID.

**Response:**
```json
{
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com",
    "create_datetime": "2023-01-01 10:00:00"
}
```

##### POST /api.php/contacts/
Creates new contact.

**Request Body:**
```json
{
    "name": "Jane Doe",
    "email": "jane@example.com"
}
```

**Response:**
```json
{
    "id": 2,
    "name": "Jane Doe",
    "email": "jane@example.com"
}
```

#### Custom API Methods

Creating custom API methods:

```php
class myappApiController extends waJsonController
{
    public function customAction()
    {
        $data = $this->processCustomLogic();
        $this->response($data);
    }
}
```

Access via: `GET /api.php/myapp/custom`

---

## Site App Specific APIs

### Site Routing

The Site app manages routing and page rendering.

#### waRouting

Routing configuration and URL generation.

##### Methods

###### `waRouting::getUrl()`
Generates URL for route.

**Syntax:**
```php
public static function getUrl($route, $params = array(), $domain = null)
```

**Parameters:**
- `$route` (string): Route pattern
- `$params` (array): Route parameters
- `$domain` (string): Domain name

**Example:**
```php
$url = waRouting::getUrl('frontend/page', array('url' => 'about'));
```

#### Site Pages

##### sitePageModel

Model for managing site pages.

**Methods:**

###### `sitePageModel::getByUrl()`
Gets page by URL.

**Syntax:**
```php
public function getByUrl($url, $domain_id = null)
```

**Parameters:**
- `$url` (string): Page URL
- `$domain_id` (int): Domain ID

**Returns:** `array` - Page data

**Example:**
```php
$page_model = new sitePageModel();
$page = $page_model->getByUrl('about-us');

if ($page) {
    echo $page['title'];
    echo $page['content'];
}
```

###### `sitePageModel::add()`
Creates new page.

**Syntax:**
```php
public function add($data)
```

**Parameters:**
- `$data` (array): Page data

**Example:**
```php
$page_id = $page_model->add(array(
    'name' => 'About Us',
    'title' => 'About Our Company',
    'content' => '<p>Page content here...</p>',
    'url' => 'about-us',
    'status' => 1
));
```

#### Site Domains

##### siteDomainModel

Model for managing domains.

**Methods:**

###### `siteDomainModel::getByName()`
Gets domain by name.

**Syntax:**
```php
public function getByName($name)
```

**Example:**
```php
$domain_model = new siteDomainModel();
$domain = $domain_model->getByName('example.com');
```

#### Theme Management

##### siteTheme

Theme management utilities.

**Methods:**

###### `siteTheme::get()`
Gets theme information.

**Syntax:**
```php
public static function get($theme_id)
```

###### `siteTheme::apply()`
Applies theme to domain.

**Syntax:**
```php
public static function apply($theme_id, $domain_id)
```

**Example:**
```php
// Get available themes
$themes = siteTheme::getList();

// Apply theme
siteTheme::apply('mytheme', $domain_id);
```

---

## Examples & Best Practices

### Creating a Simple App

#### 1. App Structure
```
myapp/
  lib/
    config/
      app.php           # App configuration
      install.php       # Installation script
    myappConfig.class.php
  actions/
    backend/
      BackendIndex.action.php
    frontend/
      Index.action.php
  templates/
    actions/
      backend/
        BackendIndex.html
      Index.html
```

#### 2. App Configuration (lib/config/app.php)
```php
return array(
    'name' => 'My App',
    'description' => 'My custom application',
    'version' => '1.0.0',
    'vendor' => 'My Company',
    'frontend' => true,
    'backend' => true,
    'icon' => array(
        48 => 'img/icon48.png'
    )
);
```

#### 3. Frontend Controller (actions/frontend/Index.action.php)
```php
<?php
class myappIndexAction extends waViewAction
{
    public function execute()
    {
        $model = new myappItemModel();
        $items = $model->getAll();
        
        $this->view->assign('items', $items);
        $this->setTitle('My App');
    }
}
```

#### 4. Backend Controller (actions/backend/BackendIndex.action.php)
```php
<?php
class myappBackendIndexAction extends waViewAction
{
    public function execute()
    {
        // Check user rights
        if (!wa()->getUser()->getRights('myapp', 'backend')) {
            throw new waRightsException('Access denied');
        }
        
        $model = new myappItemModel();
        $items = $model->getAll();
        
        $this->view->assign(array(
            'items' => $items,
            'can_edit' => wa()->getUser()->getRights('myapp', 'edit')
        ));
    }
}
```

#### 5. Model (lib/model/myappItem.model.php)
```php
<?php
class myappItemModel extends waModel
{
    protected $table = 'myapp_item';
    protected $id = 'id';
    
    public function getAll($status = 'active')
    {
        return $this->select('*')
            ->where('status = ?', $status)
            ->order('create_datetime DESC')
            ->fetchAll();
    }
    
    public function getPublished()
    {
        return $this->getAll('published');
    }
    
    public function add($data)
    {
        $data['create_datetime'] = date('Y-m-d H:i:s');
        return $this->insert($data);
    }
}
```

#### 6. Frontend Template (templates/actions/Index.html)
```smarty
<div class="myapp-items">
    <h1>My App Items</h1>
    
    {foreach $items as $item}
        <div class="item">
            <h2>{$item.title|escape}</h2>
            <p>{$item.description|escape}</p>
            <small>Created: {$item.create_datetime|wa_datetime}</small>
        </div>
    {/foreach}
</div>
```

### AJAX Controller Example

```php
<?php
class myappApiSaveController extends waJsonController
{
    public function execute()
    {
        try {
            // Validate input
            $data = $this->getRequest()->post('data');
            if (empty($data['title'])) {
                throw new waException('Title is required');
            }
            
            // Save data
            $model = new myappItemModel();
            $id = $model->add($data);
            
            $this->response(array(
                'id' => $id,
                'message' => 'Item saved successfully'
            ));
            
        } catch (Exception $e) {
            $this->setError($e->getMessage());
        }
    }
}
```

### Plugin Example

#### Plugin Class (plugins/myplugin/lib/myappMyplugin.plugin.php)
```php
<?php
class myappMypluginPlugin extends waPlugin
{
    public function orderComplete($params)
    {
        $order_id = $params['order_id'];
        
        // Send notification email
        $this->sendNotification($order_id);
        
        return array('status' => 'notification_sent');
    }
    
    private function sendNotification($order_id)
    {
        $settings = $this->getSettings();
        if ($settings['notifications_enabled']) {
            // Send email logic here
        }
    }
    
    public function backendMenu()
    {
        return array(
            'name' => 'My Plugin',
            'url' => wa()->getAppUrl('myapp') . '?plugin=myplugin',
            'icon' => 'icon.png'
        );
    }
}
```

### Best Practices

#### 1. Security
```php
// Always validate and sanitize input
$email = $request->post('email', '', 'string');
if (!wa()->getValidator('email')->isValid($email)) {
    throw new waException('Invalid email');
}

// Check user rights
if (!wa()->getUser()->getRights('myapp', 'edit')) {
    throw new waRightsException('Access denied');
}

// Use prepared statements
$items = $model->select('*')->where('user_id = ?', $user_id)->fetchAll();
```

#### 2. Error Handling
```php
try {
    $result = $this->processData();
    $this->response($result);
} catch (waException $e) {
    $this->setError($e->getMessage());
} catch (Exception $e) {
    waLog::log($e->getMessage(), 'myapp/error.log');
    $this->setError('An error occurred');
}
```

#### 3. Caching
```php
$cache = wa()->getCache();
$key = 'myapp_items_' . $user_id;

$items = $cache->get($key);
if ($items === null) {
    $items = $model->getByUserId($user_id);
    $cache->set($key, $items, 3600); // Cache for 1 hour
}
```

#### 4. Internationalization
```php
// In PHP code
$message = _w('Item saved successfully');

// In templates
{"Item saved successfully"|_w}

// With parameters
{sprintf(_w('Found %d items'), $count)}
```

#### 5. Logging
```php
// Application logs
waLog::log('User action performed', 'myapp/user.log');

// Error logs
waLog::log($exception->getMessage(), 'myapp/error.log');

// Debug logs (only in debug mode)
waLog::debug($debug_data, 'myapp/debug.log');
```

This comprehensive documentation covers the main APIs, classes, and components of the Webasyst framework with practical examples and usage patterns. For more specific implementation details, refer to the framework source code and official documentation at developers.webasyst.com.