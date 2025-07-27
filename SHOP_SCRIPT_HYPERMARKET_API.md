# Shop-Script & Hypermarket Theme API Documentation

## Table of Contents
1. [Shop-Script Overview](#shop-script-overview)
2. [Product Management APIs](#product-management-apis)
3. [Order Management APIs](#order-management-apis)
4. [Customer Management APIs](#customer-management-apis)
5. [Payment & Shipping APIs](#payment--shipping-apis)
6. [Hypermarket Theme APIs](#hypermarket-theme-apis)
7. [Shopping Cart APIs](#shopping-cart-apis)
8. [Catalog & Category APIs](#catalog--category-apis)
9. [Plugin Development](#plugin-development)
10. [Frontend Templates](#frontend-templates)
11. [Mobile & Responsive Features](#mobile--responsive-features)
12. [SEO & Marketing APIs](#seo--marketing-apis)

---

## Shop-Script Overview

Shop-Script is a comprehensive e-commerce platform built on the Webasyst framework. It provides complete online store functionality with extensive APIs for product management, order processing, payments, and more.

### Core Features
- **Product Catalog**: Unlimited products with variants, categories, and features
- **Order Management**: Complete order lifecycle management
- **Payment Processing**: Multiple payment gateways support
- **Shipping**: Flexible shipping methods and calculation
- **Customer Management**: User accounts, wishlists, reviews
- **Analytics**: Built-in sales analytics and reporting
- **Multi-store**: Support for multiple storefronts

### Architecture
```
shop/
  lib/
    model/              # Data models
    config/             # Configuration
    classes/            # Core classes
  plugins/              # Payment/shipping plugins
  themes/               # Design themes
  templates/            # Backend templates
```

---

## Product Management APIs

### shopProductModel

Core model for product management.

#### Methods

##### `shopProductModel::getById()`
Gets product by ID with full data.

**Syntax:**
```php
public function getById($id, $detailed = false)
```

**Parameters:**
- `$id` (int): Product ID
- `$detailed` (bool): Include detailed information

**Returns:** `array` - Product data

**Example:**
```php
$product_model = new shopProductModel();
$product = $product_model->getById(123, true);

echo $product['name'];
echo $product['price'];
echo $product['summary'];
```

##### `shopProductModel::getByUrl()`
Gets product by URL key.

**Syntax:**
```php
public function getByUrl($url)
```

**Example:**
```php
$product = $product_model->getByUrl('iphone-14-pro');
```

##### `shopProductModel::search()`
Searches products with filters.

**Syntax:**
```php
public function search($query, $filters = array())
```

**Parameters:**
- `$query` (string): Search query
- `$filters` (array): Search filters

**Example:**
```php
$products = $product_model->search('phone', array(
    'category_id' => 5,
    'price_min' => 100,
    'price_max' => 1000,
    'in_stock' => true,
    'limit' => 20,
    'offset' => 0
));
```

##### `shopProductModel::add()`
Adds new product.

**Syntax:**
```php
public function add($data)
```

**Example:**
```php
$product_id = $product_model->add(array(
    'name' => 'iPhone 14 Pro',
    'summary' => 'Latest iPhone model',
    'description' => 'Detailed product description...',
    'price' => 999.99,
    'currency' => 'USD',
    'url' => 'iphone-14-pro',
    'category_id' => array(5, 12),
    'status' => 1,
    'meta_title' => 'iPhone 14 Pro - Buy Online',
    'meta_description' => 'Shop iPhone 14 Pro...'
));
```

### shopProductSkuModel

Model for product variants (SKUs).

#### Methods

##### `shopProductSkuModel::getByProduct()`
Gets all SKUs for a product.

**Syntax:**
```php
public function getByProduct($product_id)
```

**Example:**
```php
$sku_model = new shopProductSkuModel();
$skus = $sku_model->getByProduct(123);

foreach ($skus as $sku) {
    echo $sku['name'] . ': $' . $sku['price'];
}
```

### shopProductStockModel

Model for inventory management.

#### Methods

##### `shopProductStockModel::getStock()`
Gets stock information.

**Syntax:**
```php
public function getStock($product_id, $sku_id = null)
```

**Example:**
```php
$stock_model = new shopProductStockModel();
$stock = $stock_model->getStock(123, 456);

if ($stock['count'] > 0) {
    echo "In stock: " . $stock['count'];
} else {
    echo "Out of stock";
}
```

##### `shopProductStockModel::reserve()`
Reserves stock for order.

**Syntax:**
```php
public function reserve($product_id, $sku_id, $quantity)
```

---

## Order Management APIs

### shopOrderModel

Core model for order management.

#### Methods

##### `shopOrderModel::getById()`
Gets order by ID.

**Syntax:**
```php
public function getById($id)
```

**Returns:** `array` - Order data

**Example:**
```php
$order_model = new shopOrderModel();
$order = $order_model->getById(12345);

echo "Order #" . $order['id'];
echo "Status: " . $order['state_id'];
echo "Total: $" . $order['total'];
```

##### `shopOrderModel::create()`
Creates new order.

**Syntax:**
```php
public function create($data)
```

**Example:**
```php
$order_data = array(
    'contact_id' => 123,
    'items' => array(
        array(
            'product_id' => 456,
            'sku_id' => 789,
            'quantity' => 2,
            'price' => 99.99
        )
    ),
    'shipping' => array(
        'rate_id' => 'flat.default',
        'rate' => 9.99
    ),
    'payment' => array(
        'method_id' => 'paypal'
    ),
    'billing_address' => array(
        'firstname' => 'John',
        'lastname' => 'Doe',
        'email' => 'john@example.com',
        'phone' => '+1234567890',
        'address' => '123 Main St',
        'city' => 'New York',
        'region' => 'NY',
        'zip' => '10001',
        'country' => 'usa'
    ),
    'shipping_address' => array(
        // Same structure as billing_address
    )
);

$order_id = $order_model->create($order_data);
```

##### `shopOrderModel::updateState()`
Updates order state.

**Syntax:**
```php
public function updateState($order_id, $state_id, $comment = null)
```

**Example:**
```php
// Update to processing
$order_model->updateState(12345, 'processing', 'Payment confirmed');

// Update to shipped
$order_model->updateState(12345, 'shipped', 'Tracking: ABC123');
```

### shopOrderItemsModel

Model for order items.

#### Methods

##### `shopOrderItemsModel::getByOrder()`
Gets items for an order.

**Syntax:**
```php
public function getByOrder($order_id)
```

**Example:**
```php
$items_model = new shopOrderItemsModel();
$items = $items_model->getByOrder(12345);

foreach ($items as $item) {
    echo $item['name'] . ' x' . $item['quantity'];
}
```

---

## Customer Management APIs

### shopCustomerModel

Model for customer management.

#### Methods

##### `shopCustomerModel::getInfo()`
Gets customer information.

**Syntax:**
```php
public function getInfo($contact_id)
```

**Example:**
```php
$customer_model = new shopCustomerModel();
$customer = $customer_model->getInfo(123);

echo $customer['name'];
echo $customer['email'];
echo "Orders: " . $customer['orders_count'];
echo "Total spent: $" . $customer['total_spent'];
```

##### `shopCustomerModel::getOrders()`
Gets customer orders.

**Syntax:**
```php
public function getOrders($contact_id, $limit = 10, $offset = 0)
```

**Example:**
```php
$orders = $customer_model->getOrders(123, 20, 0);
```

### shopCustomerAddressModel

Model for customer addresses.

#### Methods

##### `shopCustomerAddressModel::getByContact()`
Gets addresses for customer.

**Syntax:**
```php
public function getByContact($contact_id)
```

**Example:**
```php
$address_model = new shopCustomerAddressModel();
$addresses = $address_model->getByContact(123);

foreach ($addresses as $address) {
    echo $address['name'] . ': ' . $address['address'];
}
```

---

## Payment & Shipping APIs

### Payment Plugins

#### Base Payment Plugin Structure

```php
class shopPaymentPluginname extends shopPayment
{
    public function allowedCurrency()
    {
        return array('USD', 'EUR');
    }
    
    public function payment($payment_form_data, $order_data, $auto_submit = false)
    {
        // Process payment
        $view = wa()->getView();
        $view->assign('form_url', $this->getEndpointUrl());
        $view->assign('fields', $this->getFields($order_data));
        
        return $view->fetch($this->path . '/templates/payment.html');
    }
    
    protected function callbackInit($request)
    {
        // Handle payment callback
        $order_id = $request['order_id'];
        $order_model = new shopOrderModel();
        
        if ($this->verifyPayment($request)) {
            $order_model->updateState($order_id, 'paid');
            return array('result' => 'success');
        }
        
        return array('result' => 'error');
    }
}
```

#### Payment Plugin Configuration

**File: `lib/config/plugin.php`**
```php
return array(
    'name' => 'Payment Gateway',
    'description' => 'Payment processing via gateway',
    'version' => '1.0.0',
    'type' => shopPayment::TYPE_ONLINE,
    'partial_refund' => true,
    'partial_capture' => true,
    'fractional_quantity' => false
);
```

### Shipping Plugins

#### Base Shipping Plugin Structure

```php
class shopShippingPluginname extends shopShipping
{
    public function allowedCurrency()
    {
        return array('USD', 'EUR');
    }
    
    public function allowedWeightUnit()
    {
        return 'kg';
    }
    
    public function calculate()
    {
        $rates = array();
        $weight = $this->getTotalWeight();
        $price = $this->getTotalPrice();
        
        // Calculate shipping rates
        if ($weight <= 1) {
            $rates['standard'] = array(
                'name' => 'Standard Shipping',
                'rate' => 5.99,
                'currency' => 'USD'
            );
        }
        
        return $rates;
    }
    
    public function tracking($tracking_id = null)
    {
        // Return tracking information
        return array(
            'url' => 'https://tracking.example.com/' . $tracking_id,
            'description' => 'Track your package'
        );
    }
}
```

---

## Hypermarket Theme APIs

### Theme Structure

The Hypermarket theme is a sophisticated e-commerce theme for Shop-Script with modern design and responsive layout.

```
themes/hypermarket/
  theme.xml             # Theme configuration
  index.html           # Homepage template
  product.html         # Product page template
  category.html        # Category page template
  cart.html           # Shopping cart template
  checkout.html       # Checkout template
  css/
    style.css         # Main stylesheet
    mobile.css        # Mobile styles
  js/
    script.js         # Theme JavaScript
  img/                # Theme images
```

### Theme Configuration (theme.xml)

```xml
<?xml version="1.0" encoding="utf-8"?>
<theme id="hypermarket" app="shop">
    <name>Hypermarket</name>
    <description>Professional e-commerce theme</description>
    <version>2.0.0</version>
    <vendor>Webasyst</vendor>
    
    <settings>
        <setting var="primary_color" title="Primary Color" type="color" value="#007cba"/>
        <setting var="logo" title="Logo" type="file" value=""/>
        <setting var="products_per_page" title="Products per page" type="select" value="12">
            <option value="8">8</option>
            <option value="12">12</option>
            <option value="24">24</option>
        </setting>
        <setting var="show_sidebar" title="Show sidebar" type="checkbox" value="1"/>
    </settings>
</theme>
```

### Theme Template Functions

#### Product Display Functions

##### `{shopProductUrl}`
Generates product URL.

**Syntax:**
```smarty
{shopProductUrl product=$product}
```

**Example:**
```smarty
<a href="{shopProductUrl product=$product}">
    {$product.name|escape}
</a>
```

##### `{shopProductImage}`
Displays product image.

**Syntax:**
```smarty
{shopProductImage product=$product size="200x200"}
```

**Example:**
```smarty
<img src="{shopProductImage product=$product size='400x400'}" 
     alt="{$product.name|escape}">
```

##### `{shopProductPrice}`
Formats product price.

**Syntax:**
```smarty
{shopProductPrice product=$product}
```

**Example:**
```smarty
<span class="price">
    {if $product.compare_price}
        <del>{shopProductPrice price=$product.compare_price}</del>
    {/if}
    {shopProductPrice product=$product}
</span>
```

#### Category Functions

##### `{shopCategoryUrl}`
Generates category URL.

**Example:**
```smarty
<a href="{shopCategoryUrl category=$category}">
    {$category.name|escape}
</a>
```

##### `{shopCategoryTree}`
Displays category tree.

**Example:**
```smarty
{shopCategoryTree assign="categories"}
<ul class="category-menu">
    {foreach $categories as $category}
        <li>
            <a href="{shopCategoryUrl category=$category}">
                {$category.name|escape}
            </a>
            {if $category.childs}
                <ul>
                    {foreach $category.childs as $child}
                        <li>
                            <a href="{shopCategoryUrl category=$child}">
                                {$child.name|escape}
                            </a>
                        </li>
                    {/foreach}
                </ul>
            {/if}
        </li>
    {/foreach}
</ul>
```

### Responsive Design Features

#### Mobile-First Approach

```css
/* Mobile styles (default) */
.product-grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: 1rem;
}

/* Tablet styles */
@media (min-width: 768px) {
    .product-grid {
        grid-template-columns: repeat(2, 1fr);
    }
}

/* Desktop styles */
@media (min-width: 1024px) {
    .product-grid {
        grid-template-columns: repeat(3, 1fr);
    }
}

/* Large desktop */
@media (min-width: 1200px) {
    .product-grid {
        grid-template-columns: repeat(4, 1fr);
    }
}
```

#### Touch-Friendly Elements

```css
.btn {
    min-height: 44px;
    min-width: 44px;
    padding: 12px 24px;
    border-radius: 4px;
    cursor: pointer;
}

.product-image {
    position: relative;
    overflow: hidden;
}

.product-image:hover .product-overlay {
    opacity: 1;
    transform: translateY(0);
}
```

---

## Shopping Cart APIs

### shopCartModel

Model for shopping cart management.

#### Methods

##### `shopCartModel::addItem()`
Adds item to cart.

**Syntax:**
```php
public function addItem($product_id, $sku_id, $quantity, $services = array())
```

**Example:**
```php
$cart_model = new shopCartModel();
$cart_model->addItem(123, 456, 2, array(
    'gift_wrap' => array(
        'id' => 'wrap1',
        'name' => 'Gift Wrap',
        'price' => 2.99
    )
));
```

##### `shopCartModel::getItems()`
Gets cart items.

**Syntax:**
```php
public function getItems()
```

**Example:**
```php
$items = $cart_model->getItems();
$total = 0;

foreach ($items as $item) {
    echo $item['name'] . ' x' . $item['quantity'];
    $total += $item['price'] * $item['quantity'];
}

echo "Total: $" . $total;
```

##### `shopCartModel::updateItem()`
Updates cart item quantity.

**Syntax:**
```php
public function updateItem($item_code, $quantity)
```

##### `shopCartModel::deleteItem()`
Removes item from cart.

**Syntax:**
```php
public function deleteItem($item_code)
```

### Frontend Cart Implementation

```smarty
{* Add to cart form *}
<form class="add-to-cart-form" data-product-id="{$product.id}">
    {if $product.skus|@count > 1}
        <div class="sku-selection">
            <label>Choose variant:</label>
            <select name="sku_id" required>
                {foreach $product.skus as $sku}
                    <option value="{$sku.id}" 
                            data-price="{$sku.price}"
                            {if !$sku.available}disabled{/if}>
                        {$sku.name} - {shopProductPrice sku=$sku}
                        {if !$sku.available}(Out of stock){/if}
                    </option>
                {/foreach}
            </select>
        </div>
    {else}
        <input type="hidden" name="sku_id" value="{$product.skus.0.id}">
    {/if}
    
    <div class="quantity-selection">
        <label>Quantity:</label>
        <input type="number" name="quantity" value="1" min="1" max="{$product.stock}">
    </div>
    
    <button type="submit" class="btn-add-to-cart">
        Add to Cart
    </button>
</form>

{* Cart widget *}
<div class="cart-widget">
    <a href="{$wa_url}checkout/" class="cart-link">
        <span class="cart-icon">🛒</span>
        <span class="cart-count">{$wa->shop->cart->count()}</span>
        <span class="cart-total">{$wa->shop->cart->total()|wa_currency}</span>
    </a>
</div>
```

### AJAX Cart Updates

```javascript
$(document).on('submit', '.add-to-cart-form', function(e) {
    e.preventDefault();
    
    var form = $(this);
    var data = form.serialize();
    
    $.post('?action=cart&method=add', data, function(response) {
        if (response.status === 'ok') {
            updateCartWidget(response.data);
            showNotification('Item added to cart');
        } else {
            showError(response.errors);
        }
    }, 'json');
});

function updateCartWidget(cartData) {
    $('.cart-count').text(cartData.count);
    $('.cart-total').text(cartData.total);
}
```

---

## Catalog & Category APIs

### shopCategoryModel

Model for category management.

#### Methods

##### `shopCategoryModel::getTree()`
Gets category tree structure.

**Syntax:**
```php
public function getTree($parent_id = 0, $depth = null)
```

**Example:**
```php
$category_model = new shopCategoryModel();
$categories = $category_model->getTree();

foreach ($categories as $category) {
    echo str_repeat('- ', $category['depth']) . $category['name'];
}
```

##### `shopCategoryModel::getProducts()`
Gets products in category.

**Syntax:**
```php
public function getProducts($category_id, $filters = array())
```

**Example:**
```php
$products = $category_model->getProducts(5, array(
    'sort' => 'price',
    'order' => 'ASC',
    'limit' => 20,
    'offset' => 0,
    'features' => array(
        'brand' => 'Apple',
        'color' => 'Black'
    )
));
```

### shopFeatureModel

Model for product features/attributes.

#### Methods

##### `shopFeatureModel::getValues()`
Gets feature values for products.

**Syntax:**
```php
public function getValues($product_ids, $feature_id = null)
```

**Example:**
```php
$feature_model = new shopFeatureModel();
$features = $feature_model->getValues(array(123, 456));

foreach ($features as $product_id => $product_features) {
    echo "Product $product_id features:";
    foreach ($product_features as $feature) {
        echo $feature['name'] . ': ' . $feature['value'];
    }
}
```

### Product Filtering

```smarty
{* Filter form *}
<form class="product-filters" method="get">
    {* Price filter *}
    <div class="filter-group">
        <label>Price Range</label>
        <input type="range" name="price_min" min="0" max="1000" value="{$smarty.get.price_min|default:0}">
        <input type="range" name="price_max" min="0" max="1000" value="{$smarty.get.price_max|default:1000}">
    </div>
    
    {* Feature filters *}
    {foreach $filters.features as $feature}
        <div class="filter-group">
            <label>{$feature.name}</label>
            {if $feature.type == 'select'}
                <select name="features[{$feature.id}]">
                    <option value="">All</option>
                    {foreach $feature.values as $value}
                        <option value="{$value.id}" 
                                {if $smarty.get.features[$feature.id] == $value.id}selected{/if}>
                            {$value.value} ({$value.count})
                        </option>
                    {/foreach}
                </select>
            {elseif $feature.type == 'checkbox'}
                {foreach $feature.values as $value}
                    <label>
                        <input type="checkbox" 
                               name="features[{$feature.id}][]" 
                               value="{$value.id}"
                               {if in_array($value.id, (array)$smarty.get.features[$feature.id])}checked{/if}>
                        {$value.value} ({$value.count})
                    </label>
                {/foreach}
            {/if}
        </div>
    {/foreach}
    
    <button type="submit">Apply Filters</button>
</form>
```

---

## Plugin Development

### Shop-Script Plugin Structure

```
plugins/
  myplugin/
    lib/
      config/
        plugin.php      # Plugin configuration
        settings.php    # Plugin settings
      myappMyplugin.plugin.php
    templates/
      settings.html     # Settings template
    img/
      icon.png         # Plugin icon
```

### Plugin Configuration Example

**File: `lib/config/plugin.php`**
```php
return array(
    'name' => 'My Shop Plugin',
    'description' => 'Extends shop functionality',
    'version' => '1.0.0',
    'vendor' => 'My Company',
    'handlers' => array(
        'order_action.complete' => 'orderComplete',
        'product_save' => 'productSave',
        'frontend_head' => 'frontendHead'
    )
);
```

### Plugin Class Example

```php
<?php
class shopMypluginPlugin extends shopPlugin
{
    public function orderComplete($params)
    {
        $order = $params['order'];
        
        // Send order notification
        $this->sendOrderNotification($order);
        
        // Update inventory
        $this->updateInventory($order);
        
        return array('processed' => true);
    }
    
    public function productSave($params)
    {
        $product = $params['product'];
        
        // Sync with external system
        $this->syncProduct($product);
    }
    
    public function frontendHead($params)
    {
        $view = wa()->getView();
        return $view->fetch($this->path . '/templates/head.html');
    }
    
    private function sendOrderNotification($order)
    {
        $settings = $this->getSettings();
        
        if ($settings['notifications_enabled']) {
            $message = new waMailMessage();
            $message->setTo($settings['notification_email']);
            $message->setSubject('New Order #' . $order['id']);
            $message->setText('Order details...');
            $message->send();
        }
    }
}
```

---

## Frontend Templates

### Product Page Template

```smarty
{* Product page template *}
<div class="product-page">
    <div class="product-gallery">
        <div class="main-image">
            <img src="{shopProductImage product=$product size='600x600'}" 
                 alt="{$product.name|escape}">
        </div>
        
        {if $product.images|@count > 1}
            <div class="thumbnail-gallery">
                {foreach $product.images as $image}
                    <img src="{shopProductImage image=$image size='100x100'}" 
                         alt="{$product.name|escape}"
                         class="thumbnail">
                {/foreach}
            </div>
        {/if}
    </div>
    
    <div class="product-info">
        <h1 class="product-title">{$product.name|escape}</h1>
        
        <div class="product-price">
            {if $product.compare_price}
                <span class="compare-price">
                    {shopProductPrice price=$product.compare_price}
                </span>
            {/if}
            <span class="price">
                {shopProductPrice product=$product}
            </span>
        </div>
        
        <div class="product-description">
            {$product.description}
        </div>
        
        {* Add to cart form *}
        <form class="add-to-cart-form">
            <input type="hidden" name="product_id" value="{$product.id}">
            
            {if $product.skus|@count > 1}
                <div class="sku-options">
                    {foreach $product.skus as $sku}
                        <label>
                            <input type="radio" name="sku_id" value="{$sku.id}">
                            {$sku.name} - {shopProductPrice sku=$sku}
                        </label>
                    {/foreach}
                </div>
            {else}
                <input type="hidden" name="sku_id" value="{$product.skus.0.id}">
            {/if}
            
            <div class="quantity-input">
                <label>Quantity:</label>
                <input type="number" name="quantity" value="1" min="1">
            </div>
            
            <button type="submit" class="btn-add-to-cart">
                Add to Cart
            </button>
        </form>
        
        {* Product features *}
        {if $product.features}
            <div class="product-features">
                <h3>Specifications</h3>
                <dl>
                    {foreach $product.features as $feature}
                        <dt>{$feature.name|escape}</dt>
                        <dd>{$feature.value|escape}</dd>
                    {/foreach}
                </dl>
            </div>
        {/if}
    </div>
</div>

{* Related products *}
{if $related_products}
    <div class="related-products">
        <h2>Related Products</h2>
        <div class="product-grid">
            {foreach $related_products as $product}
                <div class="product-item">
                    <a href="{shopProductUrl product=$product}">
                        <img src="{shopProductImage product=$product size='200x200'}" 
                             alt="{$product.name|escape}">
                        <h3>{$product.name|escape}</h3>
                        <span class="price">{shopProductPrice product=$product}</span>
                    </a>
                </div>
            {/foreach}
        </div>
    </div>
{/if}
```

### Category Page Template

```smarty
{* Category page template *}
<div class="category-page">
    <div class="category-header">
        <h1>{$category.name|escape}</h1>
        {if $category.description}
            <div class="category-description">
                {$category.description}
            </div>
        {/if}
    </div>
    
    <div class="category-content">
        <aside class="sidebar">
            {* Subcategories *}
            {if $category.childs}
                <div class="subcategories">
                    <h3>Categories</h3>
                    <ul>
                        {foreach $category.childs as $child}
                            <li>
                                <a href="{shopCategoryUrl category=$child}">
                                    {$child.name|escape}
                                    <span class="count">({$child.count})</span>
                                </a>
                            </li>
                        {/foreach}
                    </ul>
                </div>
            {/if}
            
            {* Filters *}
            {include file="filters.html"}
        </aside>
        
        <main class="products-section">
            <div class="products-header">
                <div class="products-count">
                    {$products_count} products found
                </div>
                
                <div class="sort-options">
                    <select name="sort" onchange="location.href=this.value">
                        <option value="?sort=name">Name</option>
                        <option value="?sort=price" {if $smarty.get.sort=='price'}selected{/if}>
                            Price: Low to High
                        </option>
                        <option value="?sort=price_desc" {if $smarty.get.sort=='price_desc'}selected{/if}>
                            Price: High to Low
                        </option>
                        <option value="?sort=create_datetime" {if $smarty.get.sort=='create_datetime'}selected{/if}>
                            Newest
                        </option>
                    </select>
                </div>
            </div>
            
            <div class="product-grid">
                {foreach $products as $product}
                    <div class="product-item">
                        <a href="{shopProductUrl product=$product}" class="product-link">
                            <div class="product-image">
                                <img src="{shopProductImage product=$product size='300x300'}" 
                                     alt="{$product.name|escape}">
                                {if $product.badge}
                                    <span class="badge badge-{$product.badge}">{$product.badge}</span>
                                {/if}
                            </div>
                            
                            <div class="product-info">
                                <h3 class="product-name">{$product.name|escape}</h3>
                                
                                <div class="product-price">
                                    {if $product.compare_price}
                                        <span class="compare-price">
                                            {shopProductPrice price=$product.compare_price}
                                        </span>
                                    {/if}
                                    <span class="price">
                                        {shopProductPrice product=$product}
                                    </span>
                                </div>
                            </div>
                        </a>
                        
                        <div class="product-actions">
                            <button class="btn-quick-add" data-product-id="{$product.id}">
                                Quick Add
                            </button>
                        </div>
                    </div>
                {/foreach}
            </div>
            
            {* Pagination *}
            {if $pagination.total > $pagination.limit}
                <div class="pagination">
                    {for $page=1 to $pagination.pages}
                        {if $page == $pagination.current}
                            <span class="current">{$page}</span>
                        {else}
                            <a href="?page={$page}">{$page}</a>
                        {/if}
                    {/for}
                </div>
            {/if}
        </main>
    </div>
</div>
```

---

## Mobile & Responsive Features

### Mobile-Optimized Navigation

```smarty
{* Mobile navigation *}
<nav class="mobile-nav">
    <button class="nav-toggle" aria-label="Toggle navigation">
        <span></span>
        <span></span>
        <span></span>
    </button>
    
    <div class="nav-menu">
        <div class="nav-header">
            <img src="{$wa_theme_url}img/logo.png" alt="Logo" class="logo">
            <button class="nav-close">&times;</button>
        </div>
        
        <ul class="nav-list">
            {shopCategoryTree assign="categories"}
            {foreach $categories as $category}
                <li class="nav-item {if $category.childs}has-children{/if}">
                    <a href="{shopCategoryUrl category=$category}">
                        {$category.name|escape}
                    </a>
                    {if $category.childs}
                        <button class="submenu-toggle">+</button>
                        <ul class="submenu">
                            {foreach $category.childs as $child}
                                <li>
                                    <a href="{shopCategoryUrl category=$child}">
                                        {$child.name|escape}
                                    </a>
                                </li>
                            {/foreach}
                        </ul>
                    {/if}
                </li>
            {/foreach}
        </ul>
    </div>
    
    <div class="nav-overlay"></div>
</nav>
```

### Touch-Friendly Product Gallery

```javascript
// Touch-enabled product gallery
class ProductGallery {
    constructor(element) {
        this.gallery = element;
        this.images = element.querySelectorAll('.gallery-image');
        this.thumbnails = element.querySelectorAll('.thumbnail');
        this.currentIndex = 0;
        
        this.bindEvents();
    }
    
    bindEvents() {
        // Touch events for swiping
        let startX = 0;
        let startY = 0;
        
        this.gallery.addEventListener('touchstart', (e) => {
            startX = e.touches[0].clientX;
            startY = e.touches[0].clientY;
        });
        
        this.gallery.addEventListener('touchend', (e) => {
            const endX = e.changedTouches[0].clientX;
            const endY = e.changedTouches[0].clientY;
            
            const diffX = startX - endX;
            const diffY = startY - endY;
            
            // Horizontal swipe
            if (Math.abs(diffX) > Math.abs(diffY) && Math.abs(diffX) > 50) {
                if (diffX > 0) {
                    this.nextImage();
                } else {
                    this.prevImage();
                }
            }
        });
        
        // Thumbnail clicks
        this.thumbnails.forEach((thumb, index) => {
            thumb.addEventListener('click', () => {
                this.showImage(index);
            });
        });
    }
    
    showImage(index) {
        this.images[this.currentIndex].classList.remove('active');
        this.thumbnails[this.currentIndex].classList.remove('active');
        
        this.currentIndex = index;
        
        this.images[this.currentIndex].classList.add('active');
        this.thumbnails[this.currentIndex].classList.add('active');
    }
    
    nextImage() {
        const nextIndex = (this.currentIndex + 1) % this.images.length;
        this.showImage(nextIndex);
    }
    
    prevImage() {
        const prevIndex = (this.currentIndex - 1 + this.images.length) % this.images.length;
        this.showImage(prevIndex);
    }
}

// Initialize galleries
document.addEventListener('DOMContentLoaded', () => {
    document.querySelectorAll('.product-gallery').forEach(gallery => {
        new ProductGallery(gallery);
    });
});
```

---

## SEO & Marketing APIs

### SEO Optimization

#### Meta Tags Management

```php
// In controller
class shopFrontendProductController extends shopFrontendController
{
    public function execute()
    {
        $product_model = new shopProductModel();
        $product = $product_model->getById($this->getId());
        
        // Set meta tags
        $this->view->assign(array(
            'meta_title' => $product['meta_title'] ?: $product['name'],
            'meta_description' => $product['meta_description'] ?: waUtils::excerpt($product['summary'], 160),
            'meta_keywords' => $product['meta_keywords'],
            'canonical_url' => shopProductUrl($product)
        ));
        
        // JSON-LD structured data
        $structured_data = array(
            '@context' => 'https://schema.org',
            '@type' => 'Product',
            'name' => $product['name'],
            'description' => $product['summary'],
            'image' => shopProductImage($product, '600x600'),
            'offers' => array(
                '@type' => 'Offer',
                'price' => $product['price'],
                'priceCurrency' => $product['currency'],
                'availability' => $product['count'] > 0 ? 'InStock' : 'OutOfStock'
            )
        );
        
        $this->view->assign('structured_data', json_encode($structured_data));
    }
}
```

#### URL Generation

```php
// Custom URL patterns
function shopProductUrl($product, $absolute = false)
{
    $url = $product['category_url'] . '/' . $product['url'] . '/';
    
    if ($absolute) {
        return wa()->getRouteUrl('shop/frontend', array(), true) . $url;
    }
    
    return $url;
}

// In template
{shopProductUrl product=$product absolute=true}
```

### Analytics Integration

```smarty
{* Google Analytics Enhanced Ecommerce *}
<script>
// Product view tracking
gtag('event', 'view_item', {
    'currency': '{$product.currency}',
    'value': {$product.price},
    'items': [{
        'item_id': '{$product.id}',
        'item_name': '{$product.name|escape:"javascript"}',
        'category': '{$product.category.name|escape:"javascript"}',
        'quantity': 1,
        'price': {$product.price}
    }]
});

// Add to cart tracking
function trackAddToCart(product, quantity) {
    gtag('event', 'add_to_cart', {
        'currency': product.currency,
        'value': product.price * quantity,
        'items': [{
            'item_id': product.id,
            'item_name': product.name,
            'category': product.category,
            'quantity': quantity,
            'price': product.price
        }]
    });
}

// Purchase tracking (on thank you page)
{if $order}
gtag('event', 'purchase', {
    'transaction_id': '{$order.id}',
    'value': {$order.total},
    'currency': '{$order.currency}',
    'items': [
        {foreach $order.items as $item}
        {
            'item_id': '{$item.product_id}',
            'item_name': '{$item.name|escape:"javascript"}',
            'category': '{$item.category|escape:"javascript"}',
            'quantity': {$item.quantity},
            'price': {$item.price}
        }{if !$item@last},{/if}
        {/foreach}
    ]
});
{/if}
</script>
```

This comprehensive documentation covers the main APIs and features of Shop-Script and the Hypermarket theme, providing developers with the tools needed to build and customize e-commerce solutions on the Webasyst platform.