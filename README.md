# Assignment Framework Programming Blazor APP
# By Erlangga Wahyu Utomo (5025201118)

## The Layout (Session 1)
. layout can be following the code :

```
<div class="main">
    <ul class="pizza-cards">
        @if (specials != null)
        {
            @foreach (var special in specials)
            {
                <li style="background-image: url('@special.ImageUrl')">
                    <div class="pizza-info">
                        <span class="title">@special.Name</span>
                        @special.Description
                        <span class="price">@special.GetFormattedBasePrice()</span>
                    </div>
                </li>
            }
        }
    </ul>
</div>
```
> The Result will be
![Asignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/Session1-Layout.png)

## Customize the Pizza (Session 2)

. we build the range when we choose the pizza. The code like data binding

```
<form class="dialog-body">
    <div>
        <label>Size:</label>
        <input type="range" min="@Pizza.MinimumSize" max="@Pizza.MaximumSize" step="1" />
        <span class="size-label">
            @(Pizza.Size)" (£@(Pizza.GetFormattedTotalPrice()))
        </span>
    </div>
</form>
```
![Asignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/CustomizePizza.png)

. We can also take the order with the total price. The total price code can be following :

```
<div class="sidebar">
    @if (order.Pizzas.Any())
    {
        <div class="order-contents">
            <h2>Your order</h2>

            @foreach (var configuredPizza in order.Pizzas)
            {
                <ConfiguredPizzaItem Pizza="configuredPizza" OnRemoved="@(() => RemoveConfiguredPizza(configuredPizza))" />
            }
        </div>
    }
    else
    {
        <div class="empty-cart">Choose a pizza<br>to get started</div>
    }

    <div class="order-total @(order.Pizzas.Any() ? "" : "hidden")">
        Total:
        <span class="total-price">@order.GetFormattedTotalPrice()</span>
        <button class="btn btn-warning" disabled="@(order.Pizzas.Count == 0)" @onclick="PlaceOrder">
            Order >
        </button>
    </div>
</div>
```

![Assignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/Order.png)

## Show Order Status (Session 3)

. We Build the Page first

> Add navigation link into the myorder page. The code will be following

```
<div class="top-bar">
    (leave existing content in place)

    <a href="myorders" class="nav-tab">
        <img src="img/bike.svg" />
        <div>My Orders</div>
    </a>
</div>
```

![Asignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/OrderPage.png)

> Displaying the list of data. we can see the order list

```
<div class="main">
    @if (ordersWithStatus == null)
    {
        <text>Loading...</text>
    }
    else if (!ordersWithStatus.Any())
    {
        <h2>No orders placed</h2>
        <a class="btn btn-success" href="">Order some pizza</a>
    }
    else
    {
        <text>TODO: show orders</text>
    }
</div>
```
![Assignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/CreatesOrderStatus.png)

> After create the list, we try take order and get the result

![Assignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/OrderStatus.png)

> After that, we can add the details in order to customer know the order. You can copy this code to OrderDetails.razor

```
<div class="track-order-body">
    <div class="track-order-details">
        <OrderReview Order="orderWithStatus.Order" />
    </div>
</div>
```

![Assignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/OrderDetails.png)

## Refactor State Managemen (Session 4)

Create a new class called OrderState in the Client Project root directory - and register it as a scoped service in the DI container. In Blazor WebAssembly applications, services are registered in the Program class.

```
using BlazingPizza.Client;
using Microsoft.AspNetCore.Components.Web;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("#app");
builder.RootComponents.Add<HeadOutlet>("head::after");

builder.Services.AddScoped(sp => new HttpClient { BaseAddress = new Uri(builder.HostEnvironment.BaseAddress) });
builder.Services.AddScoped<OrderState>();

await builder.Build().RunAsync();
```
> Update the index page with add @inject

```
@page "/"
@inject HttpClient HttpClient
@inject OrderState OrderState
@inject NavigationManager NavigationManager
```

## Checkout with Validation (Session 5)

> Start by adding a new page component, Checkout.razor, with a @page directive matching the URL /checkout

```
<PageTitle>Blazing Pizza - Checkout</PageTitle>

<div class="main">
    <div class="checkout-cols">
        <div class="checkout-order-details">
            <h4>Review order</h4>
            <OrderReview Order="OrderState.Order" />
        </div>
    </div>

    <button class="checkout-button btn btn-warning" @onclick="PlaceOrder">
        Place order
    </button>
</div>
```

> bring customers here when they try to submit orders. Back in Index.razor, make sure you've deleted the PlaceOrder method, and then change the order submission button into a regular HTML link to the /checkout URL

```
<a href="checkout" class="@(OrderState.Order.Pizzas.Count == 0 ? "btn btn-warning disabled" : "btn btn-warning")">
    Order >
</a>
```

> Delivery Address
> Create a new component in the BlazingPizza.Client project's Shared folder called AddressEditor.razor

```
<div class="form-field">
    <label>Name:</label>
    <div>
        <input @bind="Address.Name" />
    </div>
</div>

<div class="form-field">
    <label>Line 1:</label>
    <div>
        <input @bind="Address.Line1" />
    </div>
</div>

<div class="form-field">
    <label>Line 2:</label>
    <div>
        <input @bind="Address.Line2" />
    </div>
</div>

<div class="form-field">
    <label>City:</label>
    <div>
        <input @bind="Address.City" />
    </div>
</div>

<div class="form-field">
    <label>Region:</label>
    <div>
        <input @bind="Address.Region" />
    </div>
</div>

<div class="form-field">
    <label>Postal code:</label>
    <div>
        <input @bind="Address.PostalCode" />
    </div>
</div>

@code {
    [Parameter] public Address Address { get; set; }
}
```
> The result will be like this
![Assignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/DeliveryDetails.png)

## authentication and authorization (Session 6)

. To enable the authentication services, add a call to AddApiAuthorization in Program.cs in the client project:

```
using BlazingPizza.Client;
using Microsoft.AspNetCore.Components.Web;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("#app");
builder.RootComponents.Add<HeadOutlet>("head::after");

builder.Services.AddScoped(sp => new HttpClient { BaseAddress = new Uri(builder.HostEnvironment.BaseAddress) });
builder.Services.AddScoped<OrderState>();

// Add auth services
builder.Services.AddApiAuthorization();

await builder.Build().RunAsync();
```

> To orchestrate the authentication flow, add an Authentication component to the Pages directory in the client project:
```
@page "/authentication/{action}"

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

> Display the Login Register Page
> Create a new component called LoginDisplay in the client project's Shared folder

```
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<div class="user-info">
    <AuthorizeView>
        <Authorizing>
            <text>...</text>
        </Authorizing>
        <Authorized>
            <img src="img/user.svg" />
            <div>
                <a href="authentication/profile" class="username">@context.User.Identity.Name</a>
                <button class="btn btn-link sign-out" @onclick="BeginSignOut">Sign out</button>
            </div>
        </Authorized>
        <NotAuthorized>
            <a class="sign-in" href="authentication/register">Register</a>
            <a class="sign-in" href="authentication/login">Log in</a>
        </NotAuthorized>
    </AuthorizeView>
</div>

@code{
    async Task BeginSignOut()
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

> put the LoginDisplay in the Main Page

```
<div class="top-bar">
    (... leave existing content in place ...)

    <LoginDisplay />
</div>
```

The Register Page 
![Assignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/RegisterPage.png)

The Register confirmation email
![Assignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/RegisterConfirm.png)

The Confirmation email success
![Assignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/ConfirmEmail.png)

Then, Back to LoginPage and the login with your account
![Assignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/LoginPage.png)

In main page, there will be your account in the top right corner
![Assignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/AccountSignIn.png)

## Java Interop (Session 7)

> Open Map.razor

```
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div id="@elementId" style="height: 100%; width: 100%;"></div>

@code {
    string elementId = $"map-{Guid.NewGuid().ToString("D")}";
    
    [Parameter] double Zoom { get; set; }
    [Parameter] List<Marker> Markers { get; set; }

    protected async override Task OnAfterRenderAsync(bool firstRender)
    {
        await JSRuntime.InvokeVoidAsync(
            "deliveryMap.showOrUpdate",
            elementId,
            Markers);
    }
}
```

> Code above to add the map in the browser with JSRuntime </br>
when you're in the order details. you can look the map your order where it is
![Assignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/MapDelivery.png)

## Template Components (Session 8)

> Open the project file by double-clicking on the BlazingComponents project name in Solution explorer

```
<Project Sdk="Microsoft.NET.Sdk.Razor">

  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <RazorLangVersion>3.0</RazorLangVersion>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Components" Version="3.1.3" />
    <PackageReference Include="Microsoft.AspNetCore.Components.Web" Version="3.1.3" />
  </ItemGroup>

</Project>
```
![Assignment_1](https://github.com/Erlangga28/Assignment-Framework-PizzaBlazing/blob/main/Result/BlazingComponent.png)

> Writing template dialog </br>
Put the following markup inside TemplatedDialog.razor:

```
<div class="dialog-container">
    <div class="dialog">

    </div>
</div>
```

> add a parameter called ChildContent of type RenderFragment. Then, update the markup to render the ChildContent in the middle of the markup. add a parameter of type bool called Show

```
@if (Show)
{
    <div class="dialog-container">
        <div class="dialog">
            @ChildContent
        </div>
    </div>
}

@code {
    [Parameter] public RenderFragment ChildContent { get; set; }
    [Parameter] public bool Show { get; set; }
}
```

