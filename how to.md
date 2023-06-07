<!DOCTYPE html>
<html>
<head>
    <style>
        body {
            background-color: rgb(62, 95, 138);
        }

        .container {
            display: flex;
            flex-direction: column;
            align-items: center;
            margin-top: 50px;
        }

        .welcome {
            font-size: 24px;
            margin-bottom: 20px;
        }

        .order-button {
            padding: 10px 20px;
            background-color: #FFA500;
            color: #ffffff;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        .menu {
            display: none;
            margin-top: 20px;
        }

        .menu-item {
            display: flex;
            justify-content: space-between;
            margin-bottom: 10px;
        }

        .menu-item-name {
            margin-right: 10px;
        }

        .add-to-cart-button {
            padding: 5px 10px;
            background-color: #4CAF50;
            color: #ffffff;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        .cart-icon {
            position: fixed;
            top: 20px;
            right: 20px;
            font-size: 24px;
            cursor: pointer;
        }

        .cart {
            display: none;
            position: fixed;
            top: 50px;
            right: 20px;
            padding: 10px;
            background-color: #f1f1f1;
            border: 1px solid #ccc;
            border-radius: 5px;
            max-height: 200px;
            overflow-y: auto;
        }

        .cart h3 {
            font-size: 18px;
            margin-top: 0;
            margin-bottom: 10px;
        }

        .cart-items {
            margin-bottom: 10px;
        }

        .cart-item {
            display: flex;
            justify-content: space-between;
            margin-bottom: 5px;
        }

        .cart-item-name {
            margin-right: 10px;
        }

        .remove-item {
            cursor: pointer;
        }

        .checkout-button {
            padding: 10px 20px;
            background-color: #FFA500;
            color: #ffffff;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        .remove-item-popup {
            display: none;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            padding: 20px;
            background-color: #f1f1f1;
            border: 1px solid #ccc;
            border-radius: 5px;
        }

        .remove-item-popup-content {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .remove-item-popup-button {
            padding: 5px 10px;
            background-color: #FF0000;
            color: #ffffff;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="welcome">Welcome to the Taco Shop!</h1>
        <button class="order-button" onclick="toggleMenu()">Place Order</button>
        <div class="menu" id="menu">
            <div class="menu-item">
                <span class="menu-item-name">🌮Tacos</span>
                <span class="menu-item-price">$2.50</span>
                <button class="add-to-cart-button" onclick="addItemToCart('Tacos', 2.50)">Add to Cart</button>
            </div>
            <div class="menu-item">
                <span class="menu-item-name">🍔Burger</span>
                <span class="menu-item-price">$5.00</span>
                <button class="add-to-cart-button" onclick="addItemToCart('Burger', 5.00)">Add to Cart</button>
            </div>
            <div class="menu-item">
                <span class="menu-item-name">🍕Pizza</span>
                <span class="menu-item-price">$8.99</span>
                <button class="add-to-cart-button" onclick="addItemToCart('Pizza', 8.99)">Add to Cart</button>
            </div>
        </div>
        <div class="cart-icon" onclick="toggleCart()">🛒</div>
        <div class="cart" id="cart">
            <h3>Cart</h3>
            <div class="cart-items" id="cart-items"></div>
            <p id="total-price"></p>
            <div class="checkout-button" onclick="checkout()">Order Now</div>
        </div>
        <div class="remove-item-popup" id="remove-item-popup">
            <div class="remove-item-popup-content">
                <label for="remove-item-quantity">Quantity:</label>
                <input type="number" id="remove-item-quantity" min="1" value="1">
                <button class="remove-item-popup-button" onclick="removeItemFromCart()">Remove</button>
                <button class="remove-item-popup-button" onclick="closeRemoveItemPopup()">Cancel</button>
            </div>
        </div>
    </div>

    <script>
        var cart = [];
        var cartItemsElement = document.getElementById('cart-items');
        var totalPriceElement = document.getElementById('total-price');

        function toggleMenu() {
            var menuElement = document.getElementById('menu');
            menuElement.style.display = menuElement.style.display === 'block' ? 'none' : 'block';
        }

        function addItemToCart(name, price) {
            var existingItem = cart.find(item => item.name === name);
            if (existingItem) {
                existingItem.quantity += 1;
            } else {
                cart.push({ name: name, price: price, quantity : 1 });
            }
            updateCart();
        }

        function removeItemFromCart(index) {
            var quantity = parseInt(document.getElementById('remove-item-quantity').value);
            if (!isNaN(quantity) && quantity > 0) {
                var item = cart[index];
                if (item) {
                    if (quantity >= item.quantity) {
                        cart.splice(index, 1);
                    } else {
                        item.quantity -= quantity;
                    }
                    updateCart();
                    closeRemoveItemPopup();
                }
            }
        }

        function updateCart() {
            var cartItemsHTML = '';
            var totalItems = 0;
            var totalPrice = 0;
            for (var i = 0; i < cart.length; i++) {
                var item = cart[i];
                var cartItemHTML = `
                    <div class="cart-item">
                        <span class="cart-item-name">${item.name}</span>
                        <span class="cart-item-quantity">${item.quantity} x</span>
                        <span class="cart-item-price">$${item.price.toFixed(2)}</span>
                        <span class="remove-item" onclick="openRemoveItemPopup(${i})">❌</span>
                    </div>
                `;
                cartItemsHTML += cartItemHTML;
                totalItems += item.quantity;
                totalPrice += item.price * item.quantity;
            }
            cartItemsElement.innerHTML = cartItemsHTML;
            totalPriceElement.innerHTML = `Total: $${totalPrice.toFixed(2)}`;
        }

        function toggleCart() {
            var cartElement = document.getElementById('cart');
            cartElement.style.display = cartElement.style.display === 'block' ? 'none' : 'block';
        }

        function openRemoveItemPopup(index) {
            var removeItemPopupElement = document.getElementById('remove-item-popup');
            removeItemPopupElement.style.display = 'block';
            var removeItemQuantityElement = document.getElementById('remove-item-quantity');
            removeItemQuantityElement.value = 1;
            removeItemQuantityElement.max = cart[index].quantity;
            removeItemQuantityElement.focus();
            removeItemQuantityElement.select();
            var removeItemQuantity = parseInt(removeItemQuantityElement.value);
            if (!isNaN(removeItemQuantity) && removeItemQuantity > 0) {
                removeItemQuantityElement.value = Math.min(removeItemQuantity, cart[index].quantity);
            }
            removeItemQuantityElement.oninput = function() {
                var value = parseInt(this.value);
                if (!isNaN(value) && value > 0) {
                    this.value = Math.min(value, cart[index].quantity);
                } else {
                    this.value = '';
                }
            }
            var removeItemPopupButton = document.getElementById('remove-item-popup-button');
            removeItemPopupButton.onclick = function() {
                removeItemFromCart(index);
            }
        }

        function closeRemoveItemPopup() {
            var removeItemPopupElement = document.getElementById('remove-item-popup');
            removeItemPopupElement.style.display = 'none';
        }

        function checkout() {
            // Perform the checkout operation
            console.log("Checkout completed!");
        }
    </script>
</body>
</html>
