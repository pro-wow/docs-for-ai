# Webasyst Theme Development Complete Guide

**A comprehensive guide to developing custom themes for the Webasyst framework from scratch**

## Table of Contents

1. [Introduction](#introduction)
2. [Template Structure Overview](#template-structure-overview)
3. [PHP and Smarty Integration](#php-and-smarty-integration)
4. [Essential Blocks and Components](#essential-blocks-and-components)
5. [Overriding and Extending Components](#overriding-and-extending-components)
6. [Repository Analysis and Customization](#repository-analysis-and-customization)
7. [Hypermarket Theme Analysis](#hypermarket-theme-analysis)
8. [Dummy Theme Reference](#dummy-theme-reference)
9. [Template Functions and Code Examples](#template-functions-and-code-examples)
10. [Responsive Design and Optimization](#responsive-design-and-optimization)
11. [Best Practices and Recommendations](#best-practices-and-recommendations)

---

## Introduction

This guide provides comprehensive documentation for developing Webasyst themes from the ground up. It covers the complete workflow from understanding the framework architecture to implementing responsive, production-ready themes.

### What is Webasyst?

Webasyst is an open-source PHP framework for developing multi-user web applications with:
- **MVC Architecture**: Model-View-Controller pattern
- **Multi-app Support**: Site, Shop-Script, Blog, CRM, etc.
- **Theme System**: Flexible templating with Smarty engine
- **Plugin Architecture**: Extensible functionality
- **Multi-domain Support**: One installation, multiple sites

### Framework Requirements

```bash
# System Requirements
- Web Server: Apache, nginx, or IIS
- PHP: 7.4+
- PHP Extensions: spl, mbstring, iconv, json, gd/ImageMagick
- Database: MySQL 4.1+
```

---

## Template Structure Overview

### Theme Directory Structure

Webasyst themes follow a standardized directory structure that ensures consistency and maintainability:

```
themes/
└── your_theme_name/
    ├── theme.xml              # Theme configuration
    ├── theme.php              # Theme PHP class (optional)
    ├── css/                   # Stylesheets
    │   ├── base.css
    │   ├── mobile.css
    │   └── print.css
    ├── js/                    # JavaScript files
    │   └── theme.js
    ├── img/                   # Theme images
    │   ├── icons/
    │   └── backgrounds/
    ├── templates/             # Smarty templates
    │   ├── layouts/           # Page layouts
    │   │   ├── Default.html
    │   │   └── Homepage.html
    │   ├── actions/           # Action templates
    │   │   ├── frontend/
    │   │   └── page/
    │   ├── includes/          # Reusable components
    │   │   ├── header.html
    │   │   ├── footer.html
    │   │   └── navigation.html
    │   └── blocks/            # Block templates
    │       ├── login.html
    │       └── sidebar.html
    └── locale/                # Translations
        ├── en_US/
        └── ru_RU/
```

### Theme Configuration (theme.xml)

Every theme must include a `theme.xml` file that defines theme metadata and settings:

```xml
<?xml version="1.0" encoding="utf-8"?>
<theme id="mytheme" app="site">
    <name>My Custom Theme</name>
    <description>A custom theme for Webasyst</description>
    <author>Your Name</author>
    <vendor>Your Company</vendor>
    <version>1.0.0</version>
    <build>1</build>
    
    <!-- Theme settings -->
    <settings>
        <setting var="header_color" name="Header color" type="color" value="#ffffff"/>
        <setting var="show_sidebar" name="Show sidebar" type="checkbox" value="1"/>
        <setting var="logo_url" name="Logo URL" type="text" value=""/>
    </settings>
    
    <!-- Required files -->
    <files>
        <file path="css/base.css"/>
        <file path="js/theme.js"/>
    </files>
</theme>
```

### Layout Templates

Layout templates define the overall page structure:

**templates/layouts/Default.html:**
```html
<!DOCTYPE html>
<html lang="{$lang}">
<head>
    <meta charset="utf-8">
    <title>{$wa_title}</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    <!-- Theme CSS -->
    {wa_css theme="css/base.css"}
    {if $wa->isMobile()}
        {wa_css theme="css/mobile.css"}
    {/if}
    
    <!-- Theme settings -->
    <style>
        .header { background-color: {$theme_settings.header_color}; }
    </style>
    
    <!-- Head content -->
    {$wa->headJs()}
    {$wa->headCss()}
</head>
<body>
    <!-- Header -->
    {include file="theme:includes/header.html"}
    
    <!-- Main content -->
    <main class="main-content">
        {$content}
    </main>
    
    <!-- Sidebar -->
    {if $theme_settings.show_sidebar}
        {include file="theme:includes/sidebar.html"}
    {/if}
    
    <!-- Footer -->
    {include file="theme:includes/footer.html"}
    
    <!-- JavaScript -->
    {wa_js theme="js/theme.js"}
    {$wa->js()}
</body>
</html>
```

---

## PHP and Smarty Integration

### Understanding the Separation

Webasyst uses a clear separation between PHP backend logic and Smarty frontend templating:

**PHP Code Usage:**
- Controller actions
- Model operations
- Business logic
- Data processing
- API interactions

**Smarty Template Usage:**
- HTML markup
- Template logic (loops, conditions)
- Variable output
- Template includes
- Theme-specific presentation

### PHP in Theme Files

**Theme PHP Class (theme.php):**
```php
<?php

class siteTheme extends waTheme
{
    /**
     * Theme initialization
     */
    public function init()
    {
        // Theme-specific initialization
        $this->addCss('css/base.css');
        $this->addJs('js/theme.js');
    }
    
    /**
     * Custom theme method
     */
    public function getThemeSettings()
    {
        return array(
            'header_color' => $this->settings['header_color'],
            'show_sidebar' => $this->settings['show_sidebar'],
            'logo_url' => $this->settings['logo_url']
        );
    }
    
    /**
     * Process theme variables
     */
    public function assignVars($view)
    {
        $view->assign('theme_settings', $this->getThemeSettings());
        $view->assign('current_year', date('Y'));
    }
}
```

### Smarty Template Functions

**Basic Smarty Usage:**
```smarty
{* Variables *}
{$variable_name}
{$array.key}
{$object->property}

{* Conditions *}
{if $condition}
    Content if true
{elseif $other_condition}
    Content if other condition true
{else}
    Content if false
{/if}

{* Loops *}
{foreach $items as $item}
    <div>{$item.name}</div>
{/foreach}

{* Includes *}
{include file="theme:includes/component.html"}
{include file="theme:blocks/login.html" assign_var="value"}

{* Template functions *}
{wa_url action="page" id=$page.id}
{wa_date date=$item.datetime}
{_w}Localized text{/_w}
```

### Advanced Template Integration

**Controller Action with Theme Integration:**
```php
<?php

class sitePageAction extends siteViewAction
{
    public function execute()
    {
        $page_id = waRequest::param('id');
        $page_model = new sitePageModel();
        $page = $page_model->getById($page_id);
        
        if (!$page) {
            throw new waException('Page not found', 404);
        }
        
        // Assign variables to template
        $this->view->assign('page', $page);
        $this->view->assign('meta_title', $page['title']);
        $this->view->assign('meta_description', $page['description']);
        
        // Set page title
        wa()->getResponse()->setTitle($page['title']);
        
        // Set template
        $this->setTemplate('page');
    }
}
```

**Corresponding Template (templates/actions/frontend/Page.html):**
```html
{* Page template *}
<article class="page">
    <header class="page-header">
        <h1 class="page-title">{$page.title|escape}</h1>
        {if $page.create_datetime}
            <time class="page-date" datetime="{$page.create_datetime|wa_date:'%Y-%m-%d'}">
                {$page.create_datetime|wa_date}
            </time>
        {/if}
    </header>
    
    <div class="page-content">
        {$page.content}
    </div>
    
    {if $page.tags}
        <footer class="page-footer">
            <div class="page-tags">
                {foreach $page.tags as $tag}
                    <a href="{wa_url action='tag' tag=$tag.name}" class="tag">
                        {$tag.name|escape}
                    </a>
                {/foreach}
            </div>
        </footer>
    {/if}
</article>
```

---

## Essential Blocks and Components

### Login/Authorization Form

**templates/blocks/login.html:**
```html
{* Login form block *}
<div class="login-block">
    {if $wa->getUser()->isAuth()}
        {* Authenticated user *}
        <div class="user-info">
            <span class="user-name">
                {_w}Hello{/_w}, {$wa->getUser()->getName()|escape}!
            </span>
            <a href="{wa_url app='webasyst' action='logout'}" class="logout-link">
                {_w}Logout{/_w}
            </a>
        </div>
    {else}
        {* Login form *}
        <form action="{wa_url app='webasyst' action='login'}" method="post" class="login-form">
            <div class="form-group">
                <label for="login-email">{_w}Email{/_w}</label>
                <input type="email" id="login-email" name="login" required 
                       class="form-control" placeholder="{_w}Enter your email{/_w}">
            </div>
            
            <div class="form-group">
                <label for="login-password">{_w}Password{/_w}</label>
                <input type="password" id="login-password" name="password" required 
                       class="form-control" placeholder="{_w}Enter password{/_w}">
            </div>
            
            <div class="form-group">
                <label class="checkbox">
                    <input type="checkbox" name="remember" value="1">
                    {_w}Remember me{/_w}
                </label>
            </div>
            
            <button type="submit" class="btn btn-primary">
                {_w}Login{/_w}
            </button>
            
            <a href="{wa_url app='webasyst' action='forgotpassword'}" class="forgot-link">
                {_w}Forgot password?{/_w}
            </a>
        </form>
    {/if}
</div>
```

### Navigation Menu

**templates/includes/navigation.html:**
```html
{* Main navigation menu *}
<nav class="main-navigation">
    <ul class="nav-menu">
        <li class="nav-item {if $wa->currentAction() == 'homepage'}active{/if}">
            <a href="{wa_url}" class="nav-link">
                {_w}Home{/_w}
            </a>
        </li>
        
        {* Get pages for menu *}
        {$pages = $wa->getApp('site')->getPages()}
        {foreach $pages as $page}
            {if $page.status == 1 && $page.in_menu}
                <li class="nav-item {if $wa->currentAction() == 'page' && $wa->getRequest()->param('id') == $page.id}active{/if}">
                    <a href="{wa_url action='page' id=$page.id}" class="nav-link">
                        {$page.title|escape}
                    </a>
                </li>
            {/if}
        {/foreach}
        
        {* Blog link (if blog app is installed) *}
        {if $wa->appExists('blog')}
            <li class="nav-item">
                <a href="{wa_url app='blog'}" class="nav-link">
                    {_w}Blog{/_w}
                </a>
            </li>
        {/if}
        
        {* Shop link (if shop app is installed) *}
        {if $wa->appExists('shop')}
            <li class="nav-item">
                <a href="{wa_url app='shop'}" class="nav-link">
                    {_w}Shop{/_w}
                </a>
            </li>
        {/if}
    </ul>
</nav>

{* Mobile menu toggle *}
<button class="mobile-menu-toggle" type="button" data-toggle="mobile-menu">
    <span class="sr-only">{_w}Toggle navigation{/_w}</span>
    <span class="hamburger-line"></span>
    <span class="hamburger-line"></span>
    <span class="hamburger-line"></span>
</button>
```

### User Account Dashboard

**templates/actions/frontend/Account.html:**
```html
{* User account dashboard *}
<div class="account-dashboard">
    <header class="account-header">
        <h1>{_w}My Account{/_w}</h1>
        <div class="user-avatar">
            {if $user.photo}
                <img src="{$user.photo}" alt="{$user.name|escape}" class="avatar-img">
            {else}
                <div class="avatar-placeholder">
                    {$user.name|truncate:1:''}
                </div>
            {/if}
        </div>
    </header>
    
    <div class="account-content">
        <div class="account-sidebar">
            <nav class="account-nav">
                <ul class="nav-list">
                    <li class="nav-item {if $active_tab == 'profile'}active{/if}">
                        <a href="{wa_url action='account' tab='profile'}" class="nav-link">
                            {_w}Profile{/_w}
                        </a>
                    </li>
                    <li class="nav-item {if $active_tab == 'settings'}active{/if}">
                        <a href="{wa_url action='account' tab='settings'}" class="nav-link">
                            {_w}Settings{/_w}
                        </a>
                    </li>
                    {if $wa->appExists('shop')}
                        <li class="nav-item {if $active_tab == 'orders'}active{/if}">
                            <a href="{wa_url action='account' tab='orders'}" class="nav-link">
                                {_w}My Orders{/_w}
                            </a>
                        </li>
                    {/if}
                </ul>
            </nav>
        </div>
        
        <div class="account-main">
            {if $active_tab == 'profile'}
                {include file="theme:blocks/account/profile.html"}
            {elseif $active_tab == 'settings'}
                {include file="theme:blocks/account/settings.html"}
            {elseif $active_tab == 'orders'}
                {include file="theme:blocks/account/orders.html"}
            {/if}
        </div>
    </div>
</div>
```

### Access Level Visibility

**templates/includes/admin-panel.html:**
```html
{* Admin panel - visible only to authorized users *}
{if $wa->getUser()->isAuth() && $wa->getUser()->getRights('site', 'edit')}
    <div class="admin-panel">
        <div class="admin-toolbar">
            <a href="{wa_url app='webasyst'}" class="admin-link">
                {_w}Admin{/_w}
            </a>
            
            {if $wa->currentApp() == 'site' && $page}
                <a href="{wa_url app='site' action='edit' id=$page.id}" class="edit-link">
                    {_w}Edit Page{/_w}
                </a>
            {/if}
            
            <div class="admin-user">
                {$wa->getUser()->getName()|escape}
                <a href="{wa_url app='webasyst' action='logout'}" class="logout-link">
                    {_w}Logout{/_w}
                </a>
            </div>
        </div>
    </div>
{/if}
```

**Role-based Content:**
```html
{* Content based on user roles *}
{$user_rights = $wa->getUser()->getRights()}

{if $user_rights.site.edit}
    <div class="editor-tools">
        <button class="btn btn-edit">{_w}Edit{/_w}</button>
    </div>
{/if}

{if $user_rights.site.admin}
    <div class="admin-tools">
        <button class="btn btn-delete">{_w}Delete{/_w}</button>
    </div>
{/if}

{* Public content for all users *}
<div class="public-content">
    {$content}
</div>

{* Members-only content *}
{if $wa->getUser()->isAuth()}
    <div class="members-content">
        <h3>{_w}Members Only{/_w}</h3>
        <p>{_w}This content is visible only to registered users.{/_w}</p>
    </div>
{/if}
```

---

## Overriding and Extending Components

### Template Override Hierarchy

Webasyst follows a template override system that allows themes to customize any part of the application:

```
Priority Order (highest to lowest):
1. wa-content/themes/[theme]/[app]/templates/
2. wa-apps/[app]/themes/[theme]/templates/
3. wa-apps/[app]/templates/
4. wa-system/[component]/templates/
```

### Overriding Core Templates

**Step 1: Identify the template to override**
```bash
# Original template location
wa-apps/site/templates/actions/frontend/Page.html

# Theme override location
wa-content/themes/mytheme/site/templates/actions/frontend/Page.html
```

**Step 2: Create the override structure**
```bash
mkdir -p wa-content/themes/mytheme/site/templates/actions/frontend/
cp wa-apps/site/templates/actions/frontend/Page.html \
   wa-content/themes/mytheme/site/templates/actions/frontend/
```

**Step 3: Customize the template**
```html
{* Custom page template override *}
<article class="custom-page">
    {* Add custom header *}
    <div class="custom-header">
        <h1 class="custom-title">{$page.title|escape}</h1>
        <div class="custom-meta">
            Published: {$page.create_datetime|wa_date}
        </div>
    </div>
    
    {* Original content with modifications *}
    <div class="custom-content">
        {$page.content}
    </div>
    
    {* Add custom footer *}
    <footer class="custom-footer">
        <div class="share-buttons">
            {include file="theme:blocks/social-share.html"}
        </div>
    </footer>
</article>
```

### Extending with Hooks and Events

**Theme hook implementation:**
```php
<?php
// wa-content/themes/mytheme/theme.php

class siteThemeMyTheme extends waTheme
{
    public function init()
    {
        // Add event listeners
        wa()->event('site.page.output', array($this, 'pageOutput'));
        wa()->event('frontend.layout', array($this, 'layoutExtend'));
    }
    
    /**
     * Modify page output
     */
    public function pageOutput($params)
    {
        $page = $params['page'];
        
        // Add reading time calculation
        $word_count = str_word_count(strip_tags($page['content']));
        $reading_time = ceil($word_count / 200); // 200 words per minute
        
        $this->view->assign('reading_time', $reading_time);
    }
    
    /**
     * Extend layout functionality
     */
    public function layoutExtend($params)
    {
        // Add custom CSS based on page type
        if (isset($params['page']) && $params['page']['layout']) {
            $layout_css = 'css/layouts/' . $params['page']['layout'] . '.css';
            if (file_exists($this->path . '/' . $layout_css)) {
                $this->addCss($layout_css);
            }
        }
    }
}
```

### Plugin Integration in Themes

**Theme plugin integration:**
```html
{* templates/includes/plugins.html *}

{* Social sharing plugin *}
{if $wa->appExists('social')}
    <div class="social-plugins">
        {wa_plugin app="social" plugin="share" page_url=$smarty.server.REQUEST_URI}
    </div>
{/if}

{* Comments plugin *}
{if $wa->appExists('comments')}
    <div class="comments-section">
        {wa_plugin app="comments" item_type="page" item_id=$page.id}
    </div>
{/if}

{* Contact form plugin *}
{if $wa->appExists('contacts')}
    <div class="contact-form">
        {wa_plugin app="contacts" plugin="form" form_id="contact"}
    </div>
{/if}
```

---

## Repository Analysis and Customization

### Analyzing wa-apps/site/themes Structure

The site app themes repository structure provides the foundation for understanding theme development:

```bash
# Repository structure analysis
wa-apps/site/themes/
├── default/                    # Default theme
│   ├── theme.xml              # Theme configuration
│   ├── css/
│   │   ├── base.css           # Base styles
│   │   └── mobile.css         # Mobile styles
│   ├── js/
│   │   └── theme.js           # Theme JavaScript
│   ├── templates/
│   │   ├── layouts/           # Page layouts
│   │   ├── actions/           # Action templates
│   │   └── includes/          # Reusable components
│   └── img/                   # Theme images
└── custom/                     # Custom themes follow same structure
```

### Step-by-Step Audit Process

**Step 1: Repository Structure Analysis**
```bash
# Clone and examine the repository
git clone https://github.com/webasyst/webasyst-framework.git
cd webasyst-framework/wa-apps/site/themes

# List available themes
ls -la

# Examine default theme structure
tree default/
```

**Step 2: Template Analysis Script**
```php
<?php
/**
 * Theme analysis script
 * Place in: wa-scripts/audit-theme.php
 */

// Initialize Webasyst
$wa_path = dirname(__FILE__) . '/../';
require_once $wa_path . 'wa-system/autoload/waAutoload.php';
waSystem::getInstance($wa_path, null, true);

// Analyze theme structure
function analyzeTheme($theme_path, $theme_name) {
    $analysis = array(
        'name' => $theme_name,
        'path' => $theme_path,
        'templates' => array(),
        'css_files' => array(),
        'js_files' => array(),
        'config' => array()
    );
    
    // Read theme.xml
    $config_file = $theme_path . '/theme.xml';
    if (file_exists($config_file)) {
        $xml = simplexml_load_file($config_file);
        $analysis['config'] = array(
            'id' => (string)$xml['id'],
            'name' => (string)$xml->name,
            'version' => (string)$xml->version,
            'author' => (string)$xml->author
        );
    }
    
    // Scan templates
    $templates_path = $theme_path . '/templates';
    if (is_dir($templates_path)) {
        $iterator = new RecursiveIteratorIterator(
            new RecursiveDirectoryIterator($templates_path)
        );
        
        foreach ($iterator as $file) {
            if ($file->isFile() && $file->getExtension() === 'html') {
                $relative_path = str_replace($templates_path . '/', '', $file->getPathname());
                $analysis['templates'][] = $relative_path;
            }
        }
    }
    
    // Scan CSS files
    $css_path = $theme_path . '/css';
    if (is_dir($css_path)) {
        $css_files = glob($css_path . '/*.css');
        $analysis['css_files'] = array_map('basename', $css_files);
    }
    
    // Scan JS files
    $js_path = $theme_path . '/js';
    if (is_dir($js_path)) {
        $js_files = glob($js_path . '/*.js');
        $analysis['js_files'] = array_map('basename', $js_files);
    }
    
    return $analysis;
}

// Audit all themes in site app
$themes_path = 'wa-apps/site/themes';
$themes = array();

if (is_dir($themes_path)) {
    $dirs = glob($themes_path . '/*', GLOB_ONLYDIR);
    foreach ($dirs as $dir) {
        $theme_name = basename($dir);
        $themes[$theme_name] = analyzeTheme($dir, $theme_name);
    }
}

// Output analysis
header('Content-Type: application/json');
echo json_encode($themes, JSON_PRETTY_PRINT);
```

**Step 3: Template Customization Checklist**

```markdown
## Theme Customization Checklist

### 1. Configuration Review
- [ ] theme.xml settings and metadata
- [ ] Required files and dependencies
- [ ] Theme settings and variables

### 2. Layout Templates
- [ ] Default.html - main layout
- [ ] Homepage.html - homepage layout
- [ ] Error.html - error pages
- [ ] Print.html - print layout

### 3. Action Templates
- [ ] frontend/Page.html - page display
- [ ] frontend/Homepage.html - homepage
- [ ] frontend/Search.html - search results
- [ ] frontend/404.html - not found page

### 4. Include Templates
- [ ] header.html - page header
- [ ] footer.html - page footer
- [ ] navigation.html - main menu
- [ ] sidebar.html - sidebar content

### 5. Block Templates
- [ ] login.html - authentication
- [ ] search.html - search form
- [ ] breadcrumbs.html - navigation breadcrumbs
- [ ] pagination.html - page navigation

### 6. Styling and Assets
- [ ] base.css - main styles
- [ ] mobile.css - responsive styles
- [ ] print.css - print styles
- [ ] theme.js - theme JavaScript
- [ ] Images and icons

### 7. Functionality Testing
- [ ] Navigation works correctly
- [ ] Forms submit properly
- [ ] Mobile responsiveness
- [ ] Cross-browser compatibility
- [ ] Performance optimization
```

---

## Hypermarket Theme Analysis

### Hypermarket Theme Overview

The Hypermarket theme (https://github.com/webasyst/hypermarket-theme) is a professional e-commerce theme that demonstrates advanced Webasyst theme development techniques.

### Key Features Analysis

**1. Multi-App Support:**
```
hypermarket-theme/
├── shop/themes/hypermarket/       # Shop-Script theme
├── site/themes/hypermarket/       # Site app theme  
├── blog/themes/hypermarket/       # Blog app theme
├── photos/themes/hypermarket/     # Photos app theme
├── helpdesk/themes/hypermarket/   # Helpdesk app theme
├── hub/themes/hypermarket/        # Hub app theme
└── mailer/themes/hypermarket/     # Mailer app theme
```

**2. Shop-Script Integration:**
```html
{* shop/themes/hypermarket/templates/layouts/Default.html *}
<!DOCTYPE html>
<html lang="{$lang}">
<head>
    <meta charset="utf-8">
    <title>{$wa_title}</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    {* Shop-specific meta tags *}
    {if $page && $page.meta_keywords}
        <meta name="keywords" content="{$page.meta_keywords|escape}">
    {/if}
    {if $page && $page.meta_description}
        <meta name="description" content="{$page.meta_description|escape}">
    {/if}
    
    {* Structured data for products *}
    {if $product}
        <script type="application/ld+json">
        {
            "@context": "http://schema.org",
            "@type": "Product",
            "name": "{$product.name|escape:'javascript'}",
            "description": "{$product.summary|strip_tags|escape:'javascript'}",
            "sku": "{$product.sku|escape:'javascript'}",
            "offers": {
                "@type": "Offer",
                "price": "{$product.price}",
                "priceCurrency": "{$shop_currency.code}"
            }
        }
        </script>
    {/if}
    
    {* Theme styles *}
    {wa_css theme="css/shop.css"}
    {wa_css theme="css/responsive.css"}
</head>
<body class="shop-layout">
    {* Header with cart *}
    {include file="theme:includes/header.html"}
    
    {* Main content *}
    <main class="shop-main">
        {$content}
    </main>
    
    {* Footer *}
    {include file="theme:includes/footer.html"}
    
    {* JavaScript *}
    {wa_js theme="js/shop.js"}
</body>
</html>
```

**3. Product Display Templates:**
```html
{* shop/themes/hypermarket/templates/Product.html *}
<div class="product-page">
    <div class="product-gallery">
        {if $product.images}
            <div class="product-images">
                {foreach $product.images as $image}
                    <img src="{$image.url_crop}" alt="{$product.name|escape}" 
                         class="product-image" data-zoom="{$image.url_big}">
                {/foreach}
            </div>
        {/if}
    </div>
    
    <div class="product-info">
        <h1 class="product-title">{$product.name|escape}</h1>
        
        <div class="product-price">
            {if $product.compare_price > $product.price}
                <span class="price-compare">{shopPrice currency=$currency price=$product.compare_price}</span>
            {/if}
            <span class="price-current">{shopPrice currency=$currency price=$product.price}</span>
        </div>
        
        <div class="product-summary">
            {$product.summary}
        </div>
        
        {* Add to cart form *}
        <form class="add-to-cart" action="{$wa_app_url}cart/add/" method="post">
            <input type="hidden" name="product_id" value="{$product.id}">
            
            {if $product.skus|count > 1}
                <div class="product-variants">
                    {foreach $product.features as $feature}
                        <div class="feature-select">
                            <label>{$feature.name|escape}:</label>
                            <select name="features[{$feature.id}]" required>
                                {foreach $feature.values as $value}
                                    <option value="{$value.id}">{$value.value|escape}</option>
                                {/foreach}
                            </select>
                        </div>
                    {/foreach}
                </div>
            {/if}
            
            <div class="quantity-select">
                <label>{_w}Quantity{/_w}:</label>
                <input type="number" name="quantity" value="1" min="1" class="quantity-input">
            </div>
            
            <button type="submit" class="btn btn-primary btn-add-cart">
                {_w}Add to Cart{/_w}
            </button>
        </form>
    </div>
</div>
```

**4. Responsive Design Implementation:**
```css
/* hypermarket/css/responsive.css */

/* Mobile-first approach */
.product-page {
    display: flex;
    flex-direction: column;
    gap: 1rem;
}

.product-gallery {
    order: 1;
}

.product-info {
    order: 2;
}

/* Tablet styles */
@media (min-width: 768px) {
    .product-page {
        flex-direction: row;
        gap: 2rem;
    }
    
    .product-gallery {
        flex: 1;
    }
    
    .product-info {
        flex: 1;
    }
}

/* Desktop styles */
@media (min-width: 1024px) {
    .product-page {
        gap: 3rem;
    }
    
    .product-gallery {
        flex: 2;
    }
    
    .product-info {
        flex: 1;
    }
}
```

### Advanced Features Implementation

**1. AJAX Cart Integration:**
```javascript
// hypermarket/js/shop.js
class HypermarketCart {
    constructor() {
        this.initEventListeners();
        this.updateCartCount();
    }
    
    initEventListeners() {
        // Add to cart
        $(document).on('submit', '.add-to-cart', (e) => {
            e.preventDefault();
            this.addToCart($(e.target));
        });
        
        // Remove from cart
        $(document).on('click', '.remove-item', (e) => {
            e.preventDefault();
            this.removeFromCart($(e.target).data('id'));
        });
    }
    
    addToCart(form) {
        const formData = form.serialize();
        
        $.post(form.attr('action'), formData)
            .done((response) => {
                if (response.status === 'ok') {
                    this.showNotification('Product added to cart');
                    this.updateCartCount();
                    this.updateCartDropdown();
                }
            })
            .fail(() => {
                this.showNotification('Error adding product to cart', 'error');
            });
    }
    
    updateCartCount() {
        $.get(shopUrl + 'cart/count/')
            .done((response) => {
                $('.cart-count').text(response.count);
            });
    }
    
    showNotification(message, type = 'success') {
        const notification = $(`
            <div class="notification notification-${type}">
                ${message}
            </div>
        `);
        
        $('body').append(notification);
        setTimeout(() => notification.remove(), 3000);
    }
}

// Initialize cart functionality
$(document).ready(() => {
    new HypermarketCart();
});
```

**2. SEO Optimization:**
```html
{* SEO optimized product template *}
<article itemscope itemtype="http://schema.org/Product" class="product">
    <h1 itemprop="name" class="product-title">{$product.name|escape}</h1>
    
    <div itemprop="offers" itemscope itemtype="http://schema.org/Offer">
        <span itemprop="price" content="{$product.price}">{shopPrice price=$product.price}</span>
        <meta itemprop="priceCurrency" content="{$currency.code}">
        <meta itemprop="availability" content="http://schema.org/InStock">
    </div>
    
    <div itemprop="description" class="product-description">
        {$product.description}
    </div>
    
    {if $product.images}
        {foreach $product.images as $image}
            <img itemprop="image" src="{$image.url}" alt="{$product.name|escape}">
        {/foreach}
    {/if}
</article>
```

---

## Dummy Theme Reference

### Dummy Theme as Development Foundation

The dummy theme (https://github.com/webasyst/dummy-theme) serves as a barebones template for developers, providing the minimal structure needed for theme development.

### Structure Analysis

```
dummy-theme/
├── site/themes/dummy/
│   ├── theme.xml                  # Basic theme configuration
│   ├── css/
│   │   └── base.css              # Minimal base styles
│   ├── templates/
│   │   ├── layouts/
│   │   │   └── Default.html      # Basic layout
│   │   └── includes/
│   │       ├── header.html       # Simple header
│   │       └── footer.html       # Simple footer
│   └── js/
│       └── theme.js              # Basic JavaScript
├── shop/themes/dummy/            # Shop-specific templates
├── blog/themes/dummy/            # Blog-specific templates
└── [other apps]/themes/dummy/    # Other app templates
```

### Best Practices from Dummy Theme

**1. Clean Theme Configuration:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<theme id="dummy" app="site">
    <name>Dummy Theme</name>
    <description>Barebones theme for developers</description>
    <author>Webasyst</author>
    <version>1.0.0</version>
    <build>1</build>
    
    <!-- Minimal settings -->
    <settings>
        <setting var="show_header" name="Show header" type="checkbox" value="1"/>
        <setting var="show_footer" name="Show footer" type="checkbox" value="1"/>
    </settings>
</theme>
```

**2. Semantic HTML Structure:**
```html
{* site/themes/dummy/templates/layouts/Default.html *}
<!DOCTYPE html>
<html lang="{$lang}">
<head>
    <meta charset="utf-8">
    <title>{$wa_title}</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    {wa_css theme="css/base.css"}
</head>
<body>
    {if $theme_settings.show_header}
        {include file="theme:includes/header.html"}
    {/if}
    
    <main role="main">
        {$content}
    </main>
    
    {if $theme_settings.show_footer}
        {include file="theme:includes/footer.html"}
    {/if}
    
    {wa_js theme="js/theme.js"}
</body>
</html>
```

**3. Progressive Enhancement CSS:**
```css
/* dummy/css/base.css */

/* Reset and base styles */
* {
    box-sizing: border-box;
}

body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    line-height: 1.6;
    margin: 0;
    padding: 0;
}

/* Layout */
main {
    min-height: calc(100vh - 120px);
    padding: 1rem;
}

/* Typography */
h1, h2, h3, h4, h5, h6 {
    margin-top: 0;
    line-height: 1.2;
}

/* Links */
a {
    color: #0066cc;
    text-decoration: none;
}

a:hover {
    text-decoration: underline;
}

/* Forms */
input, textarea, select, button {
    font-family: inherit;
    font-size: inherit;
}

/* Responsive images */
img {
    max-width: 100%;
    height: auto;
}

/* Utility classes */
.sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border: 0;
}
```

**4. Accessibility-First JavaScript:**
```javascript
// dummy/js/theme.js

(function() {
    'use strict';
    
    // Simple theme functionality
    const Theme = {
        init: function() {
            this.setupNavigation();
            this.setupForms();
        },
        
        setupNavigation: function() {
            // Mobile menu toggle
            const toggle = document.querySelector('.mobile-menu-toggle');
            const menu = document.querySelector('.main-navigation');
            
            if (toggle && menu) {
                toggle.addEventListener('click', function() {
                    const expanded = this.getAttribute('aria-expanded') === 'true';
                    this.setAttribute('aria-expanded', !expanded);
                    menu.classList.toggle('is-open');
                });
            }
        },
        
        setupForms: function() {
            // Basic form validation
            const forms = document.querySelectorAll('form[data-validate]');
            
            forms.forEach(function(form) {
                form.addEventListener('submit', function(e) {
                    if (!Theme.validateForm(form)) {
                        e.preventDefault();
                    }
                });
            });
        },
        
        validateForm: function(form) {
            const required = form.querySelectorAll('[required]');
            let valid = true;
            
            required.forEach(function(field) {
                if (!field.value.trim()) {
                    field.classList.add('error');
                    valid = false;
                } else {
                    field.classList.remove('error');
                }
            });
            
            return valid;
        }
    };
    
    // Initialize when DOM is ready
    if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', Theme.init.bind(Theme));
    } else {
        Theme.init();
    }
})();
```

### Building from Dummy Theme

**Step 1: Clone and Rename**
```bash
# Copy dummy theme as starting point
cp -r wa-apps/site/themes/dummy wa-content/themes/mytheme

# Update theme.xml
sed -i 's/id="dummy"/id="mytheme"/' wa-content/themes/mytheme/theme.xml
sed -i 's/Dummy Theme/My Custom Theme/' wa-content/themes/mytheme/theme.xml
```

**Step 2: Expand Structure**
```bash
# Add additional directories
mkdir -p wa-content/themes/mytheme/{templates/{blocks,actions/frontend},img,fonts}

# Create additional CSS files
touch wa-content/themes/mytheme/css/{layout,components,utilities}.css
```

**Step 3: Build Upon Foundation**
```html
{* Expanded layout based on dummy theme *}
<!DOCTYPE html>
<html lang="{$lang}" class="no-js">
<head>
    <meta charset="utf-8">
    <title>{$wa_title}</title>
    <meta name="description" content="{$wa_meta_description}">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    {* Progressive enhancement *}
    <script>document.documentElement.className = document.documentElement.className.replace('no-js', 'js');</script>
    
    {* Preload critical resources *}
    <link rel="preload" href="{$wa_theme_url}fonts/theme-font.woff2" as="font" type="font/woff2" crossorigin>
    
    {* Theme CSS *}
    {wa_css theme="css/base.css"}
    {wa_css theme="css/layout.css"}
    {wa_css theme="css/components.css"}
    
    {* Responsive CSS *}
    <link rel="stylesheet" href="{$wa_theme_url}css/mobile.css" media="(max-width: 767px)">
    <link rel="stylesheet" href="{$wa_theme_url}css/tablet.css" media="(min-width: 768px) and (max-width: 1023px)">
    <link rel="stylesheet" href="{$wa_theme_url}css/desktop.css" media="(min-width: 1024px)">
</head>
<body class="{$body_classes|default:''}">
    {* Skip navigation for accessibility *}
    <a href="#main-content" class="skip-link">{_w}Skip to main content{/_w}</a>
    
    {* Header *}
    {if $theme_settings.show_header}
        {include file="theme:includes/header.html"}
    {/if}
    
    {* Main content *}
    <main id="main-content" role="main" class="main-content">
        {$content}
    </main>
    
    {* Footer *}
    {if $theme_settings.show_footer}
        {include file="theme:includes/footer.html"}
    {/if}
    
    {* JavaScript *}
    {wa_js theme="js/theme.js"}
</body>
</html>
```

---

## Template Functions and Code Examples

### Essential Smarty Template Functions

**1. URL Generation Functions:**
```smarty
{* Basic URL generation *}
<a href="{wa_url}">{_w}Home{/_w}</a>
<a href="{wa_url action='about'}">{_w}About{/_w}</a>
<a href="{wa_url action='page' id=$page.id}">{$page.title|escape}</a>

{* App-specific URLs *}
<a href="{wa_url app='blog'}">{_w}Blog{/_w}</a>
<a href="{wa_url app='shop' action='products'}">{_w}Products{/_w}</a>

{* URLs with parameters *}
<a href="{wa_url action='search' query=$search_term}">
    {_w}Search results{/_w}
</a>

{* External URLs *}
<a href="{wa_url absolute=true action='page' id=$page.id}">
    {_w}Full URL{/_w}
</a>

{* Theme URLs *}
<img src="{$wa_theme_url}img/logo.png" alt="{_w}Logo{/_w}">
<link rel="stylesheet" href="{$wa_theme_url}css/custom.css">
```

**2. Date and Time Functions:**
```smarty
{* Basic date formatting *}
<time datetime="{$item.date|wa_date:'%Y-%m-%d'}">
    {$item.date|wa_date}
</time>

{* Custom date formats *}
{$item.date|wa_date:'%B %d, %Y'}           <!-- January 15, 2024 -->
{$item.date|wa_date:'%d.%m.%Y'}            <!-- 15.01.2024 -->
{$item.date|wa_date:'%Y-%m-%d %H:%M:%S'}   <!-- 2024-01-15 14:30:00 -->

{* Relative time *}
<span class="time-ago" title="{$item.date|wa_date}">
    {$item.date|wa_date_relative}
</span>

{* Timezone handling *}
{$item.date|wa_date:null:$user_timezone}
```

**3. Text Processing Functions:**
```smarty
{* Safe output *}
<h1>{$title|escape}</h1>
<div>{$content|escape:'html'}</div>
<script>var data = {$json_data|escape:'javascript'};</script>

{* Text manipulation *}
<p>{$description|truncate:150:'...'}</p>
<p>{$text|nl2br}</p>
<p>{$text|strip_tags}</p>

{* Word count and reading time *}
<div class="meta">
    {assign var="word_count" value=$content|strip_tags|count_words}
    <span>{_w}Words{/_w}: {$word_count}</span>
    <span>{_w}Reading time{/_w}: {($word_count/200)|ceil} {_w}min{/_w}</span>
</div>
```

**4. Localization Functions:**
```smarty
{* Basic translation *}
<h1>{_w}Welcome to our site{/_w}</h1>

{* Translation with parameters *}
<p>{sprintf(_w('Hello, %s!'), $user.name|escape)}</p>

{* Pluralization *}
<span>
    {if $count == 1}
        {sprintf(_w('%d item'), $count)}
    {else}
        {sprintf(_w('%d items'), $count)}
    {/if}
</span>

{* Language-specific content *}
{if $wa->getLocale() == 'ru_RU'}
    <p>Русский контент</p>
{else}
    <p>English content</p>
{/if}
```

### Advanced Template Patterns

**1. Component Pattern:**
```smarty
{* components/product-card.html *}
<article class="product-card" data-product-id="{$product.id}">
    <div class="product-image">
        {if $product.image}
            <img src="{$product.image.url_crop}" alt="{$product.name|escape}"
                 loading="lazy" width="300" height="300">
        {else}
            <div class="product-image-placeholder">
                {_w}No image{/_w}
            </div>
        {/if}
    </div>
    
    <div class="product-info">
        <h3 class="product-title">
            <a href="{wa_url action='product' id=$product.id}">
                {$product.name|escape}
            </a>
        </h3>
        
        <div class="product-price">
            {if $product.compare_price && $product.compare_price > $product.price}
                <span class="price-compare">{shopPrice price=$product.compare_price}</span>
            {/if}
            <span class="price-current">{shopPrice price=$product.price}</span>
        </div>
        
        {if $product.rating}
            <div class="product-rating">
                {include file="theme:components/rating-stars.html" rating=$product.rating}
            </div>
        {/if}
        
        <button class="btn btn-add-cart" data-product-id="{$product.id}">
            {_w}Add to Cart{/_w}
        </button>
    </div>
</article>

{* Usage in templates *}
{foreach $products as $product}
    {include file="theme:components/product-card.html" product=$product}
{/foreach}
```

**2. Layout Inheritance Pattern:**
```smarty
{* layouts/base.html - Base layout *}
<!DOCTYPE html>
<html lang="{$lang}">
<head>
    <meta charset="utf-8">
    <title>{block name="title"}{$wa_title}{/block}</title>
    
    {block name="meta"}
        <meta name="description" content="{$wa_meta_description}">
    {/block}
    
    {block name="css"}
        {wa_css theme="css/base.css"}
    {/block}
</head>
<body class="{block name="body_class"}{/block}">
    {block name="header"}
        {include file="theme:includes/header.html"}
    {/block}
    
    <main class="main-content">
        {block name="content"}{/block}
    </main>
    
    {block name="footer"}
        {include file="theme:includes/footer.html"}
    {/block}
    
    {block name="js"}
        {wa_js theme="js/theme.js"}
    {/block}
</body>
</html>

{* layouts/shop.html - Shop-specific layout *}
{extends file="theme:layouts/base.html"}

{block name="title"}{$product.name|escape} - {$parent}{/block}

{block name="body_class"}shop-layout{/block}

{block name="css"}
    {$parent}
    {wa_css theme="css/shop.css"}
{/block}

{block name="header"}
    {include file="theme:includes/shop-header.html"}
{/block}
```

**3. AJAX Pattern:**
```smarty
{* AJAX content wrapper *}
<div id="ajax-content" data-url="{wa_url action='ajax_content'}">
    {if $ajax_request}
        {* AJAX response content *}
        {include file="theme:components/content-partial.html"}
    {else}
        {* Initial page load *}
        <div class="loading">
            {_w}Loading...{/_w}
        </div>
    {/if}
</div>

{* JavaScript for AJAX *}
<script>
function loadAjaxContent(url, container) {
    fetch(url, {
        headers: {
            'X-Requested-With': 'XMLHttpRequest'
        }
    })
    .then(response => response.text())
    .then(html => {
        container.innerHTML = html;
    })
    .catch(error => {
        console.error('Error loading content:', error);
    });
}

// Load content on page load
document.addEventListener('DOMContentLoaded', function() {
    const container = document.getElementById('ajax-content');
    const url = container.dataset.url;
    loadAjaxContent(url, container);
});
</script>
```

### Shop-Script Specific Functions

**1. Product Functions:**
```smarty
{* Product URL generation *}
<a href="{shopProductUrl product=$product}">
    {$product.name|escape}
</a>

{* Product image display *}
{shopProductImage product=$product size="300x300" alt=$product.name class="product-img"}

{* Product price formatting *}
<span class="price">
    {shopPrice price=$product.price currency=$currency}
</span>

{* Product features *}
{if $product.features}
    <ul class="product-features">
        {foreach $product.features as $feature}
            <li>
                <strong>{$feature.name|escape}:</strong>
                {$feature.value|escape}
            </li>
        {/foreach}
    </ul>
{/if}
```

**2. Category Functions:**
```smarty
{* Category navigation *}
{function name="category_tree" categories=$categories level=0}
    <ul class="category-list level-{$level}">
        {foreach $categories as $category}
            <li class="category-item">
                <a href="{shopCategoryUrl category=$category}" 
                   class="category-link {if $current_category_id == $category.id}active{/if}">
                    {$category.name|escape}
                    {if $category.count}
                        <span class="category-count">({$category.count})</span>
                    {/if}
                </a>
                
                {if $category.subcategories}
                    {call name="category_tree" categories=$category.subcategories level=$level+1}
                {/if}
            </li>
        {/foreach}
    </ul>
{/function}

{* Usage *}
{call name="category_tree" categories=$shop_categories}
```

**3. Cart Functions:**
```smarty
{* Shopping cart widget *}
<div class="cart-widget" id="cart-widget">
    <a href="{shopUrl}cart/" class="cart-link">
        <span class="cart-icon">🛒</span>
        <span class="cart-count">{$wa->shop->cart->count()}</span>
        <span class="cart-total">{shopPrice price=$wa->shop->cart->total()}</span>
    </a>
    
    {if $wa->shop->cart->count()}
        <div class="cart-dropdown">
            {foreach $wa->shop->cart->items() as $item}
                <div class="cart-item">
                    <img src="{$item.product.image.url_thumb}" alt="{$item.product.name|escape}">
                    <div class="item-info">
                        <div class="item-name">{$item.product.name|escape}</div>
                        <div class="item-price">
                            {$item.quantity} × {shopPrice price=$item.price}
                        </div>
                    </div>
                </div>
            {/foreach}
            
            <div class="cart-actions">
                <a href="{shopUrl}cart/" class="btn btn-view-cart">
                    {_w}View Cart{/_w}
                </a>
                <a href="{shopUrl}checkout/" class="btn btn-checkout">
                    {_w}Checkout{/_w}
                </a>
            </div>
        </div>
    {/if}
</div>
```

---

## Responsive Design and Optimization

### Mobile-First CSS Architecture

**1. Base Styles (Mobile):**
```css
/* css/base.css - Mobile-first base styles */

/* CSS Variables for theming */
:root {
    --primary-color: #007bff;
    --secondary-color: #6c757d;
    --success-color: #28a745;
    --danger-color: #dc3545;
    --warning-color: #ffc107;
    --info-color: #17a2b8;
    
    --font-family-base: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    --font-size-base: 16px;
    --line-height-base: 1.6;
    
    --spacing-xs: 0.25rem;
    --spacing-sm: 0.5rem;
    --spacing-md: 1rem;
    --spacing-lg: 1.5rem;
    --spacing-xl: 3rem;
    
    --border-radius: 4px;
    --box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

/* Reset and base styles */
*,
*::before,
*::after {
    box-sizing: border-box;
}

body {
    font-family: var(--font-family-base);
    font-size: var(--font-size-base);
    line-height: var(--line-height-base);
    margin: 0;
    padding: 0;
    color: #333;
    background-color: #fff;
}

/* Typography */
h1, h2, h3, h4, h5, h6 {
    margin-top: 0;
    margin-bottom: var(--spacing-md);
    line-height: 1.2;
    font-weight: 600;
}

h1 { font-size: 2rem; }
h2 { font-size: 1.75rem; }
h3 { font-size: 1.5rem; }
h4 { font-size: 1.25rem; }
h5 { font-size: 1.125rem; }
h6 { font-size: 1rem; }

/* Layout containers */
.container {
    width: 100%;
    padding: 0 var(--spacing-md);
    margin: 0 auto;
}

.row {
    display: flex;
    flex-wrap: wrap;
    margin: 0 calc(-1 * var(--spacing-md));
}

.col {
    flex: 1;
    padding: 0 var(--spacing-md);
    min-width: 0;
}

/* Grid system */
.col-12 { width: 100%; }
.col-11 { width: 91.66667%; }
.col-10 { width: 83.33333%; }
.col-9 { width: 75%; }
.col-8 { width: 66.66667%; }
.col-7 { width: 58.33333%; }
.col-6 { width: 50%; }
.col-5 { width: 41.66667%; }
.col-4 { width: 33.33333%; }
.col-3 { width: 25%; }
.col-2 { width: 16.66667%; }
.col-1 { width: 8.33333%; }
```

**2. Component Styles:**
```css
/* css/components.css - Reusable components */

/* Buttons */
.btn {
    display: inline-block;
    padding: var(--spacing-sm) var(--spacing-md);
    font-size: var(--font-size-base);
    line-height: 1.5;
    text-align: center;
    text-decoration: none;
    border: 1px solid transparent;
    border-radius: var(--border-radius);
    cursor: pointer;
    transition: all 0.15s ease-in-out;
    user-select: none;
}

.btn:hover {
    text-decoration: none;
    transform: translateY(-1px);
}

.btn-primary {
    color: #fff;
    background-color: var(--primary-color);
    border-color: var(--primary-color);
}

.btn-primary:hover {
    background-color: #0056b3;
    border-color: #0056b3;
}

.btn-secondary {
    color: #fff;
    background-color: var(--secondary-color);
    border-color: var(--secondary-color);
}

.btn-outline-primary {
    color: var(--primary-color);
    border-color: var(--primary-color);
    background-color: transparent;
}

.btn-outline-primary:hover {
    color: #fff;
    background-color: var(--primary-color);
}

/* Forms */
.form-group {
    margin-bottom: var(--spacing-md);
}

.form-label {
    display: block;
    margin-bottom: var(--spacing-xs);
    font-weight: 500;
}

.form-control {
    display: block;
    width: 100%;
    padding: var(--spacing-sm) var(--spacing-md);
    font-size: var(--font-size-base);
    line-height: 1.5;
    color: #495057;
    background-color: #fff;
    background-clip: padding-box;
    border: 1px solid #ced4da;
    border-radius: var(--border-radius);
    transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}

.form-control:focus {
    outline: 0;
    border-color: var(--primary-color);
    box-shadow: 0 0 0 0.2rem rgba(0, 123, 255, 0.25);
}

/* Cards */
.card {
    position: relative;
    display: flex;
    flex-direction: column;
    min-width: 0;
    background-color: #fff;
    background-clip: border-box;
    border: 1px solid rgba(0,0,0,.125);
    border-radius: var(--border-radius);
    box-shadow: var(--box-shadow);
}

.card-header {
    padding: var(--spacing-md);
    background-color: rgba(0,0,0,.03);
    border-bottom: 1px solid rgba(0,0,0,.125);
}

.card-body {
    flex: 1 1 auto;
    padding: var(--spacing-md);
}

.card-footer {
    padding: var(--spacing-md);
    background-color: rgba(0,0,0,.03);
    border-top: 1px solid rgba(0,0,0,.125);
}
```

**3. Responsive Breakpoints:**
```css
/* css/responsive.css - Responsive styles */

/* Tablet styles */
@media (min-width: 768px) {
    .container {
        max-width: 750px;
    }
    
    /* Grid system for tablets */
    .col-md-12 { width: 100%; }
    .col-md-11 { width: 91.66667%; }
    .col-md-10 { width: 83.33333%; }
    .col-md-9 { width: 75%; }
    .col-md-8 { width: 66.66667%; }
    .col-md-7 { width: 58.33333%; }
    .col-md-6 { width: 50%; }
    .col-md-5 { width: 41.66667%; }
    .col-md-4 { width: 33.33333%; }
    .col-md-3 { width: 25%; }
    .col-md-2 { width: 16.66667%; }
    .col-md-1 { width: 8.33333%; }
    
    /* Hide mobile menu toggle */
    .mobile-menu-toggle {
        display: none;
    }
    
    /* Show desktop navigation */
    .main-navigation {
        display: block !important;
    }
    
    /* Horizontal navigation */
    .nav-menu {
        display: flex;
        list-style: none;
        margin: 0;
        padding: 0;
    }
    
    .nav-item {
        margin-right: var(--spacing-lg);
    }
}

/* Desktop styles */
@media (min-width: 1024px) {
    .container {
        max-width: 980px;
    }
    
    /* Grid system for desktop */
    .col-lg-12 { width: 100%; }
    .col-lg-11 { width: 91.66667%; }
    .col-lg-10 { width: 83.33333%; }
    .col-lg-9 { width: 75%; }
    .col-lg-8 { width: 66.66667%; }
    .col-lg-7 { width: 58.33333%; }
    .col-lg-6 { width: 50%; }
    .col-lg-5 { width: 41.66667%; }
    .col-lg-4 { width: 33.33333%; }
    .col-lg-3 { width: 25%; }
    .col-lg-2 { width: 16.66667%; }
    .col-lg-1 { width: 8.33333%; }
    
    /* Larger font sizes for desktop */
    h1 { font-size: 2.5rem; }
    h2 { font-size: 2rem; }
    h3 { font-size: 1.75rem; }
}

/* Large desktop styles */
@media (min-width: 1200px) {
    .container {
        max-width: 1170px;
    }
    
    /* Grid system for large desktop */
    .col-xl-12 { width: 100%; }
    .col-xl-11 { width: 91.66667%; }
    .col-xl-10 { width: 83.33333%; }
    .col-xl-9 { width: 75%; }
    .col-xl-8 { width: 66.66667%; }
    .col-xl-7 { width: 58.33333%; }
    .col-xl-6 { width: 50%; }
    .col-xl-5 { width: 41.66667%; }
    .col-xl-4 { width: 33.33333%; }
    .col-xl-3 { width: 25%; }
    .col-xl-2 { width: 16.66667%; }
    .col-xl-1 { width: 8.33333%; }
}

/* Print styles */
@media print {
    .no-print {
        display: none !important;
    }
    
    body {
        font-size: 12pt;
        line-height: 1.4;
    }
    
    h1, h2, h3 {
        page-break-after: avoid;
    }
    
    img {
        max-width: 100% !important;
    }
    
    .container {
        width: auto;
        max-width: none;
        padding: 0;
    }
}
```

### Performance Optimization

**1. Critical CSS Inlining:**
```html
{* Inline critical CSS for above-the-fold content *}
<style>
/* Critical CSS for immediate rendering */
body { font-family: sans-serif; margin: 0; }
.header { background: #fff; padding: 1rem; }
.main-content { min-height: 50vh; }
.loading { text-align: center; padding: 2rem; }
</style>

{* Load non-critical CSS asynchronously *}
<link rel="preload" href="{$wa_theme_url}css/base.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
<noscript><link rel="stylesheet" href="{$wa_theme_url}css/base.css"></noscript>
```

**2. Image Optimization:**
```html
{* Responsive images with lazy loading *}
<img src="{$image.url_thumb}" 
     srcset="{$image.url_thumb} 300w, 
             {$image.url_crop} 600w, 
             {$image.url_big} 1200w"
     sizes="(max-width: 768px) 100vw, 
            (max-width: 1024px) 50vw, 
            33vw"
     alt="{$image.alt|escape}"
     loading="lazy"
     width="300" 
     height="200">

{* WebP support with fallback *}
<picture>
    <source srcset="{$image.url_webp}" type="image/webp">
    <source srcset="{$image.url_jpg}" type="image/jpeg">
    <img src="{$image.url_jpg}" alt="{$image.alt|escape}" loading="lazy">
</picture>
```

**3. JavaScript Optimization:**
```javascript
// js/theme.js - Optimized theme JavaScript

(function() {
    'use strict';
    
    // Feature detection
    const supports = {
        intersectionObserver: 'IntersectionObserver' in window,
        webp: function() {
            const canvas = document.createElement('canvas');
            return canvas.toDataURL('image/webp').indexOf('data:image/webp') === 0;
        }(),
        localStorage: function() {
            try {
                localStorage.setItem('test', 'test');
                localStorage.removeItem('test');
                return true;
            } catch(e) {
                return false;
            }
        }()
    };
    
    // Lazy loading with IntersectionObserver
    function initLazyLoading() {
        if (!supports.intersectionObserver) {
            // Fallback for browsers without IntersectionObserver
            document.querySelectorAll('img[loading="lazy"]').forEach(img => {
                img.src = img.dataset.src || img.src;
            });
            return;
        }
        
        const imageObserver = new IntersectionObserver((entries, observer) => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    const img = entry.target;
                    img.src = img.dataset.src || img.src;
                    img.classList.remove('lazy');
                    imageObserver.unobserve(img);
                }
            });
        });
        
        document.querySelectorAll('img[loading="lazy"]').forEach(img => {
            imageObserver.observe(img);
        });
    }
    
    // Performance monitoring
    function trackPerformance() {
        if ('performance' in window && 'timing' in performance) {
            window.addEventListener('load', () => {
                setTimeout(() => {
                    const timing = performance.timing;
                    const loadTime = timing.loadEventEnd - timing.navigationStart;
                    
                    // Send performance data (example)
                    if (loadTime > 3000) {
                        console.warn('Page load time exceeds 3 seconds:', loadTime + 'ms');
                    }
                }, 0);
            });
        }
    }
    
    // Initialize theme
    function init() {
        initLazyLoading();
        trackPerformance();
        
        // Add loaded class for CSS transitions
        document.documentElement.classList.add('loaded');
    }
    
    // DOM ready
    if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', init);
    } else {
        init();
    }
})();
```

### Mobile-Specific Optimizations

**1. Touch-Friendly Interface:**
```css
/* Touch-friendly button sizes */
.btn,
.form-control,
.nav-link {
    min-height: 44px; /* iOS recommended minimum */
    min-width: 44px;
}

/* Improved tap targets */
.card,
.product-card {
    margin-bottom: var(--spacing-md);
}

.product-card .product-title a {
    display: block;
    padding: var(--spacing-sm);
    margin: calc(-1 * var(--spacing-sm));
}

/* Prevent zoom on input focus (iOS) */
.form-control {
    font-size: max(16px, 1em);
}

/* Better scrolling performance */
.scrollable {
    -webkit-overflow-scrolling: touch;
    overflow-y: auto;
}
```

**2. Mobile Navigation:**
```html
{* Mobile-optimized navigation *}
<nav class="mobile-navigation">
    <button class="mobile-menu-toggle" 
            type="button" 
            aria-expanded="false" 
            aria-controls="mobile-menu"
            aria-label="{_w}Toggle navigation{/_w}">
        <span class="hamburger-line"></span>
        <span class="hamburger-line"></span>
        <span class="hamburger-line"></span>
    </button>
    
    <div class="mobile-menu" id="mobile-menu">
        <ul class="mobile-nav-list">
            <li class="mobile-nav-item">
                <a href="{wa_url}" class="mobile-nav-link">
                    {_w}Home{/_w}
                </a>
            </li>
            {foreach $menu_items as $item}
                <li class="mobile-nav-item">
                    <a href="{$item.url}" class="mobile-nav-link">
                        {$item.title|escape}
                    </a>
                    {if $item.children}
                        <button class="submenu-toggle" 
                                aria-expanded="false"
                                aria-controls="submenu-{$item.id}">
                            <span class="sr-only">{_w}Toggle submenu{/_w}</span>
                        </button>
                        <ul class="mobile-submenu" id="submenu-{$item.id}">
                            {foreach $item.children as $child}
                                <li class="mobile-submenu-item">
                                    <a href="{$child.url}" class="mobile-submenu-link">
                                        {$child.title|escape}
                                    </a>
                                </li>
                            {/foreach}
                        </ul>
                    {/if}
                </li>
            {/foreach}
        </ul>
    </div>
</nav>
```

---

## Best Practices and Recommendations

### Development Workflow

**1. Theme Development Setup:**
```bash
# Development environment setup
mkdir webasyst-theme-dev
cd webasyst-theme-dev

# Clone Webasyst framework
git clone https://github.com/webasyst/webasyst-framework.git .

# Set up development theme
mkdir -p wa-content/themes/dev-theme
cd wa-content/themes/dev-theme

# Initialize theme structure
mkdir -p {css,js,img,templates/{layouts,actions,includes,blocks},locale}

# Create basic files
touch theme.xml css/base.css js/theme.js templates/layouts/Default.html
```

**2. Version Control Strategy:**
```bash
# Initialize git repository for theme
cd wa-content/themes/your-theme
git init

# Create .gitignore
echo "*.log
*.tmp
.DS_Store
Thumbs.db
node_modules/
.sass-cache/" > .gitignore

# Initial commit
git add .
git commit -m "Initial theme structure"

# Create development branch
git checkout -b develop
```

**3. Build Process Setup:**
```json
{
  "name": "webasyst-theme",
  "version": "1.0.0",
  "description": "Custom Webasyst theme",
  "scripts": {
    "build": "npm run build:css && npm run build:js",
    "build:css": "sass scss/main.scss css/base.css --style compressed",
    "build:js": "webpack --mode production",
    "watch": "npm run watch:css & npm run watch:js",
    "watch:css": "sass --watch scss/main.scss:css/base.css",
    "watch:js": "webpack --mode development --watch"
  },
  "devDependencies": {
    "sass": "^1.45.0",
    "webpack": "^5.65.0",
    "webpack-cli": "^4.9.0"
  }
}
```

### Security Best Practices

**1. Template Security:**
```smarty
{* Always escape user input *}
<h1>{$user_title|escape}</h1>
<div class="content">{$user_content|escape:'html'}</div>

{* Use proper escaping for different contexts *}
<script>
var config = {
    title: {$page_title|escape:'javascript'},
    url: '{$page_url|escape:'javascript'}'
};
</script>

<a href="{$user_url|escape:'url'}" title="{$user_title|escape:'html'}">
    {$link_text|escape}
</a>

{* Validate and sanitize in PHP before assigning to templates *}
```

**2. PHP Security Patterns:**
```php
<?php
// Theme controller example with security

class siteMyThemeAction extends siteViewAction
{
    public function execute()
    {
        // Validate input parameters
        $page_id = waRequest::param('id', 0, 'int');
        if ($page_id <= 0) {
            throw new waException('Invalid page ID', 404);
        }
        
        // Check user permissions
        if (!wa()->getUser()->getRights('site', 'read')) {
            throw new waRightsException('Access denied');
        }
        
        // Sanitize user input
        $search_query = waRequest::get('query', '', 'string');
        $search_query = trim(strip_tags($search_query));
        
        // Use prepared statements for database queries
        $model = new waModel();
        $results = $model->select('*')
            ->where('title LIKE ?', '%' . $model->escape($search_query, 'like') . '%')
            ->andWhere('status = ?', 'published')
            ->limit(20)
            ->fetchAll();
        
        // Assign safe data to template
        $this->view->assign('results', $results);
        $this->view->assign('search_query', htmlspecialchars($search_query));
    }
}
```

### Performance Guidelines

**1. CSS Performance:**
```css
/* Optimize CSS for performance */

/* Use efficient selectors */
.component-class { } /* Good */
div.component-class { } /* Avoid unnecessary tag selectors */
.parent .child .grandchild { } /* Avoid deep nesting */

/* Minimize repaints and reflows */
.element {
    transform: translateX(100px); /* Use transform instead of left/top */
    will-change: transform; /* Hint to browser for optimization */
}

/* Use CSS containment */
.product-card {
    contain: layout style paint;
}

/* Optimize animations */
@keyframes slideIn {
    from { transform: translateX(-100%); }
    to { transform: translateX(0); }
}

.slide-animation {
    animation: slideIn 0.3s ease-out;
}
```

**2. JavaScript Performance:**
```javascript
// Performance-optimized JavaScript patterns

// Debounce expensive operations
function debounce(func, wait) {
    let timeout;
    return function executedFunction(...args) {
        const later = () => {
            clearTimeout(timeout);
            func(...args);
        };
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
    };
}

// Use event delegation
document.addEventListener('click', function(e) {
    if (e.target.matches('.product-card .add-to-cart')) {
        handleAddToCart(e.target);
    }
});

// Optimize DOM queries
class ThemeOptimized {
    constructor() {
        this.cache = new Map();
        this.init();
    }
    
    getElement(selector) {
        if (!this.cache.has(selector)) {
            this.cache.set(selector, document.querySelector(selector));
        }
        return this.cache.get(selector);
    }
    
    init() {
        // Cache frequently used elements
        this.header = this.getElement('.header');
        this.navigation = this.getElement('.main-navigation');
        this.cart = this.getElement('.cart-widget');
    }
}
```

### Testing and Quality Assurance

**1. Cross-Browser Testing Checklist:**
```markdown
## Browser Compatibility Testing

### Desktop Browsers
- [ ] Chrome (latest 2 versions)
- [ ] Firefox (latest 2 versions)
- [ ] Safari (latest 2 versions)
- [ ] Edge (latest 2 versions)
- [ ] Internet Explorer 11 (if required)

### Mobile Browsers
- [ ] Chrome Mobile (Android)
- [ ] Safari Mobile (iOS)
- [ ] Samsung Internet
- [ ] Firefox Mobile

### Testing Points
- [ ] Layout rendering
- [ ] Navigation functionality
- [ ] Form submissions
- [ ] JavaScript interactions
- [ ] CSS animations
- [ ] Image loading
- [ ] Font rendering
```

**2. Accessibility Testing:**
```html
{* Accessibility-focused template example *}
<nav role="navigation" aria-label="{_w}Main navigation{/_w}">
    <ul class="nav-menu">
        {foreach $menu_items as $item}
            <li class="nav-item">
                <a href="{$item.url}" 
                   class="nav-link {if $current_page == $item.id}current{/if}"
                   {if $current_page == $item.id}aria-current="page"{/if}>
                    {$item.title|escape}
                </a>
            </li>
        {/foreach}
    </ul>
</nav>

{* Form with proper labels and error handling *}
<form class="contact-form" novalidate>
    <div class="form-group">
        <label for="contact-name" class="form-label">
            {_w}Name{/_w} <span class="required" aria-label="{_w}Required{/_w}">*</span>
        </label>
        <input type="text" 
               id="contact-name" 
               name="name" 
               class="form-control"
               required
               aria-describedby="name-error"
               aria-invalid="false">
        <div id="name-error" class="error-message" role="alert" aria-live="polite"></div>
    </div>
    
    <button type="submit" class="btn btn-primary">
        {_w}Send Message{/_w}
        <span class="sr-only">{_w}Contact form{/_w}</span>
    </button>
</form>

{* Skip navigation for screen readers *}
<a href="#main-content" class="skip-link">
    {_w}Skip to main content{/_w}
</a>
```

**3. Performance Monitoring:**
```javascript
// Performance monitoring script
(function() {
    'use strict';
    
    // Core Web Vitals monitoring
    function measureCoreWebVitals() {
        // Largest Contentful Paint
        new PerformanceObserver((entryList) => {
            const entries = entryList.getEntries();
            const lastEntry = entries[entries.length - 1];
            console.log('LCP:', lastEntry.startTime);
        }).observe({entryTypes: ['largest-contentful-paint']});
        
        // First Input Delay
        new PerformanceObserver((entryList) => {
            const firstInput = entryList.getEntries()[0];
            console.log('FID:', firstInput.processingStart - firstInput.startTime);
        }).observe({entryTypes: ['first-input']});
        
        // Cumulative Layout Shift
        let clsValue = 0;
        new PerformanceObserver((entryList) => {
            for (const entry of entryList.getEntries()) {
                if (!entry.hadRecentInput) {
                    clsValue += entry.value;
                }
            }
            console.log('CLS:', clsValue);
        }).observe({entryTypes: ['layout-shift']});
    }
    
    // Resource timing
    function analyzeResources() {
        const resources = performance.getEntriesByType('resource');
        const slowResources = resources.filter(resource => 
            resource.duration > 1000
        );
        
        if (slowResources.length > 0) {
            console.warn('Slow loading resources:', slowResources);
        }
    }
    
    // Initialize monitoring
    if ('PerformanceObserver' in window) {
        measureCoreWebVitals();
    }
    
    window.addEventListener('load', analyzeResources);
})();
```

### Documentation and Maintenance

**1. Theme Documentation Template:**
```markdown
# Theme Name

## Overview
Brief description of the theme, its purpose, and target audience.

## Features
- Feature 1
- Feature 2
- Feature 3

## Installation

### Requirements
- Webasyst Framework 3.7+
- PHP 7.4+
- Modern browser support

### Setup
1. Copy theme files to `wa-content/themes/theme-name/`
2. Activate theme in Site app settings
3. Configure theme settings

## Configuration

### Theme Settings
- `header_color`: Header background color (default: #ffffff)
- `show_sidebar`: Display sidebar (default: true)
- `logo_url`: Custom logo URL

### Customization
Instructions for common customizations...

## File Structure
```
theme-name/
├── theme.xml
├── css/
├── js/
├── templates/
└── img/
```

## Browser Support
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

## Changelog
See CHANGELOG.md for version history.

## Support
Contact information or support resources.
```

**2. Code Comments Standard:**
```smarty
{*
 * Product card component
 * 
 * Displays product information in a card layout
 * Used in: category pages, search results, homepage
 * 
 * Required variables:
 * - $product (array): Product data from shopProductModel
 * 
 * Optional variables:
 * - $show_description (bool): Show product description (default: false)
 * - $image_size (string): Image size variant (default: 'crop')
 *}

<article class="product-card" itemscope itemtype="http://schema.org/Product">
    {* Product image *}
    <div class="product-image">
        {if $product.images}
            <img src="{$product.images.0.url_{$image_size|default:'crop'}}" 
                 alt="{$product.name|escape}"
                 itemprop="image"
                 loading="lazy">
        {/if}
    </div>
    
    {* Product information *}
    <div class="product-info">
        <h3 class="product-title" itemprop="name">
            <a href="{shopProductUrl product=$product}">
                {$product.name|escape}
            </a>
        </h3>
        
        {* Optional description *}
        {if $show_description && $product.summary}
            <div class="product-description" itemprop="description">
                {$product.summary|truncate:100}
            </div>
        {/if}
        
        {* Price *}
        <div class="product-price" itemprop="offers" itemscope itemtype="http://schema.org/Offer">
            <span itemprop="price" content="{$product.price}">
                {shopPrice price=$product.price}
            </span>
            <meta itemprop="priceCurrency" content="{$currency.code}">
        </div>
    </div>
</article>
```

This comprehensive guide provides everything needed to develop professional Webasyst themes from scratch, covering all aspects from basic structure to advanced optimization techniques. Use this documentation as a reference for creating maintainable, performant, and accessible themes for the Webasyst platform.