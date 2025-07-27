# Webasyst Framework Complete API Documentation

Welcome to the comprehensive API documentation for the Webasyst framework, Shop-Script, and related components. This documentation provides detailed information about all public APIs, functions, components, and best practices for developing with the Webasyst ecosystem.

## 📚 Documentation Overview

This documentation set covers:

- **Webasyst Framework Core APIs** - Complete framework functionality
- **Shop-Script E-commerce APIs** - Online store development
- **Hypermarket Theme APIs** - Professional e-commerce theme
- **Plugin Development** - Extending functionality
- **Theme Development** - Custom design creation
- **Best Practices** - Development guidelines and examples

## 📖 Documentation Files

### [Webasyst Framework API Documentation](WEBASYST_API_DOCUMENTATION.md)
**Complete guide to the core Webasyst framework**

Covers:
- Core Framework Classes (`waSystem`, `waConfig`, `waRequest`, `waResponse`)
- Controller Classes (`waController`, `waViewController`, `waJsonController`)
- Model Classes (`waModel`, `waContactModel`)
- View and Template System (`waSmarty`, template functions)
- Plugin System (`waPlugin`, event handlers)
- Theme System (theme structure, configuration)
- Event System (`waEvent`, event handling)
- Authentication & Authorization (`waUser`, `waAuth`)
- Database Layer (`waDbAdapter`, query building)
- Utility Classes (`waUtils`, `waFiles`)
- REST API (endpoints, authentication)
- Site App Specific APIs (routing, pages, domains)
- Examples & Best Practices

### [Shop-Script & Hypermarket Theme API Documentation](SHOP_SCRIPT_HYPERMARKET_API.md)
**Complete guide to e-commerce development**

Covers:
- Product Management APIs (`shopProductModel`, SKUs, inventory)
- Order Management APIs (`shopOrderModel`, order lifecycle)
- Customer Management APIs (`shopCustomerModel`, addresses)
- Payment & Shipping APIs (plugin development)
- Hypermarket Theme APIs (responsive design, template functions)
- Shopping Cart APIs (`shopCartModel`, AJAX cart)
- Catalog & Category APIs (`shopCategoryModel`, filtering)
- Plugin Development (Shop-Script specific)
- Frontend Templates (product pages, category pages)
- Mobile & Responsive Features
- SEO & Marketing APIs (structured data, analytics)

## 🚀 Quick Start Guide

### System Requirements
- **Web Server**: Apache, nginx, or IIS
- **PHP**: 7.4+
- **PHP Extensions**: spl, mbstring, iconv, json, gd or ImageMagick
- **Database**: MySQL 4.1+

### Installation
```bash
# Clone the framework
git clone https://github.com/webasyst/webasyst-framework.git

# Set permissions
chmod 0775 /path/to/webasyst

# Run web installer
# Navigate to http://your-domain/webasyst/ in browser
```

### Basic Usage Examples

#### Getting Started with Framework
```php
// Get system instance
$wa = waSystem::getInstance();

// Get current user
$user = $wa->getUser();
if ($user->isAuth()) {
    echo "Welcome, " . $user->getName();
}

// Handle request
$request = waRequest::getInstance();
$page = $request->get('page', 1, 'int');
```

#### Creating a Simple Controller
```php
class myappIndexAction extends waViewAction
{
    public function execute()
    {
        $this->view->assign('title', 'My App');
        $this->view->assign('message', 'Hello, Webasyst!');
    }
}
```

#### Working with Models
```php
class myappProductModel extends waModel
{
    protected $table = 'myapp_product';
    
    public function getActive()
    {
        return $this->select('*')
            ->where('status = ?', 'active')
            ->fetchAll();
    }
}
```

## 🛠 Development Resources

### Official Links
- **Framework Repository**: https://github.com/webasyst/webasyst-framework
- **Shop-Script Repository**: https://github.com/webasyst/shop-script
- **Hypermarket Theme**: https://github.com/webasyst/hypermarket-theme
- **Developer Portal**: https://developers.webasyst.com/
- **Documentation**: https://developers.webasyst.com/docs/

### Key Components

#### Core Applications
- **Site** - Website management and routing
- **Shop-Script** - E-commerce platform
- **CRM** - Customer relationship management
- **Mailer** - Email marketing
- **Blog** - Content management
- **Photos** - Image galleries
- **Hub** - Knowledge base and discussions

#### Framework Architecture
```
wa-apps/          # Application modules
wa-config/        # Configuration files
wa-content/       # User content and uploads
wa-installer/     # Installation scripts
wa-plugins/       # Plugin modules
wa-system/        # Core framework files
wa-widgets/       # Dashboard widgets
```

## 🎯 Common Use Cases

### E-commerce Store Development
For building online stores, focus on:
- [Product Management APIs](SHOP_SCRIPT_HYPERMARKET_API.md#product-management-apis)
- [Shopping Cart APIs](SHOP_SCRIPT_HYPERMARKET_API.md#shopping-cart-apis)
- [Payment & Shipping APIs](SHOP_SCRIPT_HYPERMARKET_API.md#payment--shipping-apis)

### Custom Application Development
For building custom apps, start with:
- [Core Framework Classes](WEBASYST_API_DOCUMENTATION.md#core-framework-classes)
- [Controller Classes](WEBASYST_API_DOCUMENTATION.md#controller-classes)
- [Model Classes](WEBASYST_API_DOCUMENTATION.md#model-classes)

### Theme Development
For creating custom themes:
- [Theme System](WEBASYST_API_DOCUMENTATION.md#theme-system)
- [View and Template System](WEBASYST_API_DOCUMENTATION.md#view-and-template-system)
- [Hypermarket Theme APIs](SHOP_SCRIPT_HYPERMARKET_API.md#hypermarket-theme-apis)

### Plugin Development
For extending functionality:
- [Plugin System](WEBASYST_API_DOCUMENTATION.md#plugin-system)
- [Event System](WEBASYST_API_DOCUMENTATION.md#event-system)
- [Shop-Script Plugin Development](SHOP_SCRIPT_HYPERMARKET_API.md#plugin-development)

## 🔧 API Reference Quick Links

### Framework Core
- [`waSystem`](WEBASYST_API_DOCUMENTATION.md#wasystem) - Main system class
- [`waController`](WEBASYST_API_DOCUMENTATION.md#wacontroller) - Base controller
- [`waModel`](WEBASYST_API_DOCUMENTATION.md#wamodel) - Database operations
- [`waUser`](WEBASYST_API_DOCUMENTATION.md#wauser) - User management
- [`waRequest`](WEBASYST_API_DOCUMENTATION.md#warequest) - HTTP requests
- [`waResponse`](WEBASYST_API_DOCUMENTATION.md#waresponse) - HTTP responses

### Shop-Script Core
- [`shopProductModel`](SHOP_SCRIPT_HYPERMARKET_API.md#shopproductmodel) - Product management
- [`shopOrderModel`](SHOP_SCRIPT_HYPERMARKET_API.md#shopordermodel) - Order processing
- [`shopCartModel`](SHOP_SCRIPT_HYPERMARKET_API.md#shopcartmodel) - Shopping cart
- [`shopCategoryModel`](SHOP_SCRIPT_HYPERMARKET_API.md#shopcategorymodel) - Category management

### Template Functions
- [`{wa_url}`](WEBASYST_API_DOCUMENTATION.md#wa_url) - URL generation
- [`{shopProductUrl}`](SHOP_SCRIPT_HYPERMARKET_API.md#shopproducturl) - Product URLs
- [`{shopProductImage}`](SHOP_SCRIPT_HYPERMARKET_API.md#shopproductimage) - Product images
- [`{shopProductPrice}`](SHOP_SCRIPT_HYPERMARKET_API.md#shopproductprice) - Price formatting

## 📝 Examples and Tutorials

### Creating a Simple App
See the complete example in [Examples & Best Practices](WEBASYST_API_DOCUMENTATION.md#examples--best-practices)

### E-commerce Store Setup
Follow the [Shop-Script Overview](SHOP_SCRIPT_HYPERMARKET_API.md#shop-script-overview)

### Theme Customization
Check out [Hypermarket Theme APIs](SHOP_SCRIPT_HYPERMARKET_API.md#hypermarket-theme-apis)

### Plugin Development
Review [Plugin Development](WEBASYST_API_DOCUMENTATION.md#plugin-system) section

## 🛡 Security Best Practices

Always follow these security guidelines:

```php
// Validate input
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

## 🔍 Debugging and Logging

Enable debugging and logging:

```php
// Application logs
waLog::log('User action performed', 'myapp/user.log');

// Error logs
waLog::log($exception->getMessage(), 'myapp/error.log');

// Debug logs (only in debug mode)
waLog::debug($debug_data, 'myapp/debug.log');
```

## 🌐 Internationalization

Support multiple languages:

```php
// In PHP code
$message = _w('Item saved successfully');

// In templates
{"Item saved successfully"|_w}

// With parameters
{sprintf(_w('Found %d items'), $count)}
```

## 📦 Package Management

For modern development workflows:

```json
{
    "require": {
        "webasyst/framework": "^3.7"
    }
}
```

## 🤝 Contributing

To contribute to this documentation:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📄 License

This documentation is provided under the same license as the Webasyst framework (LGPL-3.0).

## 🆘 Support

- **Documentation Issues**: Submit an issue to this repository
- **Framework Support**: https://support.webasyst.com/
- **Developer Forums**: https://support.webasyst.com/
- **Commercial Support**: support@webasyst.com

---

**Last Updated**: January 2025  
**Framework Version**: 3.7.0+  
**Shop-Script Version**: 8.0+

For the most up-to-date information, always refer to the official Webasyst developer documentation at https://developers.webasyst.com/docs/