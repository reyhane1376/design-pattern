# Factory Method 

Factory Method is a creational design pattern that provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created.


![alt text](image.png)



## Applicability

#### Use the Factory Method when you don't know beforehand the exact types and dependencies of the objects your code should work with.   
#### Use the Factory Method when you want to provide users of your library or framework with a way to extend its internal components.   
#### Use the Factory Method when you want to save system resources by reusing existing objects instead of rebuilding them each time.

```php
namespace Src\FactoryMethod;

interface Publisher
{
    public function publish(Message $message);
}
```

```php
namespace Src\FactoryMethod;

abstract class MessagePublisher
{
    public function publish(Message $message)
    {
        $publisher = $this->createPublisher();
        $publisher->publish($message);
    }

    abstract protected function createPublisher(): Publisher;
}
```

```php
namespace Src\FactoryMethod;

class LinkedInPublisherFactory extends MessagePublisher
{
    protected function createPublisher(): Publisher
    {
        return new LinkedInPublisher();
    }
}
```
```php
namespace Src\FactoryMethod;

class LinkedInPublisher implements Publisher
{
    public function publish(Message $message)
    {
        // TODO: Implement publish() method.
    }
}
```
```php
namespace Src\FactoryMethod;

class MessagesController extends Controller
{
    private $publisher;

    /**
     * @param MessagePublisher $publisher
     */
    public function __construct(MessagePublisher $publisher)
    {
        $this->publisher = $publisher;
    }

    public function publishMessage(Request $request)
    {
        $this->publisher->publish(new Message());
    }
}
```

```php
public function register()
{
    $this->app->bind(abstract: MessagePublisher::class, concrete: LinkedInPublisherFactory::class);
}
```

# Abstract Factory

Abstract Factory is a creational design pattern that lets you produce families of related objects without specifying their concrete classes.

![alt text](image-1.png)

## Application

Use the Abstract Factory when your code needs to work with various families of related products, but you don't want it to depend on the concrete classes of those products—they might be unknown beforehand or you simply want to allow for future extensibility.

```php
namespace Src\AbstractFactory\FormBuilder\Elements;

interface Button
{
    public function render();
}
```

```php
namespace Src\AbstractFactory\FormBuilder\Elements;

interface TextInput
{
    public function render();
}
```

```php
namespace Src\AbstractFactory\FormBuilder\Elements;

interface TextArea
{
    public function render();
}
```

```php
namespace Src\AbstractFactory\FormBuilder\Elements;

interface RadioButton
{
    public function render();
}
```

```php
namespace Src\AbstractFactory\FormBuilder;

use Src\AbstractFactory\FormBuilder\Elements\Button;
use Src\AbstractFactory\FormBuilder\Elements\RadioButton;
use Src\AbstractFactory\FormBuilder\Elements\TextArea;
use Src\AbstractFactory\FormBuilder\Elements\TextInput;

interface FormUIFactory
{
    public function createButton(): Button;
    public function createTextInput(): TextInput;
    public function createTextArea(): TextArea;
    public function createRadioButton(): RadioButton;
}
```
```php
namespace Src\AbstractFactory\FormBuilder\Providers\Bootstrap\Elements;

use Src\AbstractFactory\FormBuilder\Elements\Button;

class BootstrapButton implements Button
{
    public function render()
    {
        echo '<button name="save-button" class="form-input">Button</button>';
    }
}
```

```php
namespace Src\AbstractFactory\FormBuilder\Providers\Bootstrap\Elements;

use Src\AbstractFactory\FormBuilder\Elements\TextArea;

class BootstrapTextArea implements TextArea
{
    public function render()
    {
        echo '<textarea class="form-input textarea" rows="10" cols="30">Text Area Content</textarea>';
    }
}
```

```php
namespace Src\AbstractFactory\FormBuilder\Providers\Bootstrap\Elements;

use Src\AbstractFactory\FormBuilder\Elements\RadioButton;

class BootstrapRadioButton implements RadioButton
{
    public function render()
    {
        // TODO: Implement render() method.
    }
}
```

```php
namespace Src\AbstractFactory\FormBuilder\Providers\Bootstrap\Elements;

use Src\AbstractFactory\FormBuilder\Elements\TextInput;

class BootstrapTextInput implements TextInput
{
    public function render()
    {
        // TODO: Implement render() method.
    }
}
```
### And the same for the rest of the forms ... 

```php
use Illuminate\Http\Request;

class FormBuilderController extends Controller
{
    private $formUiBuilder;

    /**
     * @param $formUiBuilder
     */
    public function __construct($formUiBuilder)
    {
        $this->formUiBuilder = $formUiBuilder;
    }

    public function output(Request $request)
    {
        $button = $this->formUiBuilder->createButton();
        $textInput = $this->formUiBuilder->createTextInput();
        $textArea = $this->formUiBuilder->createTextArea();

        return view('form', compact('button', 'textArea', 'textInput'));
    }
}
```

```php
public function register()
{
    $this->app->bind(abstract: FormUIFactory::class, concrete: BootstrapFormUiFactory::class);
}
```

# Builder 

Builder is a creational design pattern that lets you construct complex objects step by step. The pattern allows you to produce different types and representations of an object using the same construction code.

![alt text](image-2.png)

## Application

Use the Builder pattern to get rid of a "telescopic constructor".
Use the Builder pattern when you want your code to be able to create different representations of some product.   
Use the Builder to construct Composite trees or other complex objects.

```php
namespace Src\RequestBuilder;

class Request
{
    private $url;
    private $data;
    private $method;
    private $headers;
    private $agent;

    public function __construct(RequestBuilder $builder)
    {
        $this->uri = $builder->getUri();
        $this->method = $builder->getMethod();
        $this->data = $builder->getData();
        $this->headers = $builder->getHeaders();
        $this->agent = $builder->getAgent();
    }

    public function run()
    {
        $request = curl_init($this->uri);

        // ... rest of the implementation ...

        return null;
    }
}
```

```php
namespace Src\RequestBuilder;

class RequestBuilder
{
    private $uri;
    private $data;
    private $method;
    private $headers;
    private $agent;

    /**
     * @var Request
     */
    private $request;

    public static function forge(): RequestBuilder
    {
        return new RequestBuilder();
    }

    public function withUri(string $uri): RequestBuilder
    {
        $this->uri = $uri;
        return $this;
    }

    public function withData(array $data): RequestBuilder
    {
        $this->data = $data;

        return $this;
    }

    public function withHeaders(array $headers): RequestBuilder
    {
        $this->headers = $headers;

        return $this;
    }

    public function withAgent(string $agent): RequestBuilder
    {
        $this->agent = $agent;

        return $this;
    }

    public function withMethod(string $method): RequestBuilder
    {
        $this->method = $method;

        return $this;
    }

    public function getUri()
    {
        return $this->uri;
    }

    public function getData()
    {
        return $this->data;
    }

    public function getHeaders()
    {
        return $this->headers;
    }

    public function getAgents()
    {
        return $this->agents;
    }

    public function getMethod()
    {
        return $this->method;
    }

    public function build(): Request
    {
        return new Request($this);
    }


}
```

```php
namespace Src\RequestBuilder;

class ApiService
{
    public function fetchUsers()
    {
        $request = RequestBuilder::forge()
            ->withUri(uri: 'https://api-service.com/users')
            ->withMethod(method: 'GET')
            ->withData([
                'user_name' => 'some_user_name',
                'password' => 'some_password'
            ])
            ->build();

            $response = $request->run();
    }
}
```
# Adapter 

Adapter is a structural design pattern that allows objects with incompatible interfaces to collaborate.

![alt text](image-3.png)

## Application

### Use the Adapter class when you want to use some existing class, but its interface isn't compatible with the rest of your code

### Use the pattern when you want to reuse several existing subclasses that lack some common functionality that can't be added to the superclass.

```php
namespace Src\PaymentProvider;

class Invoice
{
    private $amount;

    /**
     * @param $amount
     */
    public function __construct($amount)
    {
        $this->amount = $amount;
    }

    public function amount(): int
    {
        return $this->amount;
    }
}
```

```php
namespace Src\PaymentProvider;

interface OnlineGateway
{
    public function startPay(Invoice $invoice): void;
}
```

```php
namespace Src\PaymentProvider;

class ZarnipalProvider implements OnlineGateway
{
    public function startPay(Invoice $invoice): void
    {
        // TODO: Implement startPay() method.
    }
}
```
```php
namespace Src\PaymentProvider;

class SamanBankGateway
{
    private $apiKey;

    /**
     * @param $apiKey
     */
    public function __construct($apiKey)
    {
        $this->apiKey = $apiKey;
    }

    public function pay(int $amount): void
    {
        // ...
    }
}
```

```php 
namespace Src\PaymentProvider;

class SamanGatewayAdapter implements OnlineGateway
{
    private $samanApi;

    /**
     * @param SamanBankGateway $samanApi
     */
    public function __construct(SamanBankGateway $samanApi)
    {
        $this->samanApi = $samanApi;
    }

    public function startPay(Invoice $invoice): void
    {
        $this->samanApi->pay($invoice->amount());
    }
}
```

# Bridge 

Bridge is a structural design pattern that lets you split a large class or a set of closely related classes into two separate hierarchies—abstraction and implementation—which can be developed independently of each other.

![alt text](image-4.png)

## Application

### Use the Bridge pattern when you want to divide and organize a monolithic class that has several variants of some functionality (for example, if the class can work with various database servers)

### Use the pattern when you need to extend a class in several orthogonal (independent) dimensions.

### Use the Bridge if you need to be able to switch implementations at runtime


```php
namespace Src\PaymentModule;

abstract class PaymentMethod
{
    private $handler;

    /**
     * @param PaymentHandler $handler
     */
    public function __construct(PaymentHandler $handler)
    {
        $this->handler = $handler;
    }

    abstract public function startPay(Invoice $invoice);
}
```

```php
namespace Src\PaymentModule;

interface PaymentHandler
{
    public function pay(Invoice $invoice);
}
```

```php

class OnlinePayment extends PaymentMethod
{
    private $connection;

    /**
     * @param Connection $connection
     */
    public function __construct(Connection $connection, PaymentHandler $handler)
    {
        parent::__construct($handler);
        $this->connection = $connection;
    }

    public function startPay(Invoice $invoice)
    {
        // $this->connection->insert("INSERT INTO payments");
        $this->handler->pay($invoice);
    }
}
```
```php
namespace Src\PaymentModule\Handlers;

use Src\PaymentModule\Invoice;
use Src\PaymentModule\PaymentHandler;

class SayyadChequeHandler implements PaymentHandler
{
    public function pay(Invoice $invoice)
    {
        // TODO: Implement pay() method.
    }
}
```

```php
public function register()
{
    $this->app->bind(OnlinePayment::class, function ($app) {
        $connection = resolve(ConnectionFactory::class);
        return new OnlinePayment($connection->make('mysql'), new SayyadChequeHandler());
    });
}
```
# Composite

Composite is a structural design pattern that lets you compose objects into tree structures and then work with these structures as if they were individual objects.

![alt text](image-5.png)

## Application

### Use the Composite pattern when you have to implement a tree-like object structure.

### Use the pattern when you want the client code to treat both simple and complex elements uniformly.

```php
namespace Src\Concept;

interface Component
{
    public function operation();
}
```

```php
namespace Src\Concept;

class Leaf implements Component
{
    public function operation()
    {
        // TODO: Implement operation() method.
    }
}
```

```php
namespace Src\Concept;

class Composite implements Component
{
    private $components = [];

    public function addComponent(Component $component)
    {
        $this->components[] = $component;
    }

    public function operation()
    {
        foreach ($this->components as $component) {
            $component->operation();
        }
    }
}
```

```php
namespace Src\Concept;

class Client
{
    private $component;

    /**
     * @param Component $component
     */
    public function __construct(Component $component)
    {
        $this->component = $component;
    }
}
```