import React, { useState } from "react";
import { Button } from "@/components/ui/button";
import { Card, CardContent } from "@/components/ui/card";
import { Input } from "@/components/ui/input";
import { motion } from "framer-motion";
import { products } from "@/data/products";

export default function EliteSportsHub() {
  const [selectedSize, setSelectedSize] = useState("");
  const [cartItems, setCartItems] = useState([]);
  const [checkoutMessage, setCheckoutMessage] = useState("");

  const filteredProducts = selectedSize
    ? products.filter((product) => product.sizes.includes(selectedSize))
    : products;

  const addToCart = (product) => {
    setCartItems((prev) => {
      const existingItem = prev.find((item) => item.id === product.id);
      if (existingItem) {
        return prev.map((item) =>
          item.id === product.id ? { ...item, quantity: item.quantity + 1 } : item
        );
      }
      return [...prev, { ...product, quantity: 1 }];
    });
  };

  const removeFromCart = (productId) => {
    setCartItems((prev) => prev.filter((item) => item.id !== productId));
  };

  const changeQuantity = (productId, amount) => {
    setCartItems((prev) =>
      prev.map((item) =>
        item.id === productId
          ? { ...item, quantity: Math.max(1, item.quantity + amount) }
          : item
      )
    );
  };

  const total = cartItems.reduce((sum, item) => {
    const price = parseFloat(item.price.replace("$", ""));
    return sum + price * item.quantity;
  }, 0);

  const handleCheckout = () => {
    if (cartItems.length === 0) {
      setCheckoutMessage("Your cart is empty. Please add items before checkout.");
    } else {
      setCheckoutMessage("✅ Thank you for your purchase! We'll process your order shortly.");
      setCartItems([]);
    }
  };

  return (
    <div className="p-6 max-w-7xl mx-auto">
      <motion.header
        initial={{ opacity: 0, y: -20 }}
        animate={{ opacity: 1, y: 0 }}
        transition={{ duration: 0.6 }}
        className="mb-6 text-center"
      >
        <h1 className="text-4xl font-bold text-blue-800 mb-2">
          Elite Sports Hub
        </h1>
        <p className="text-gray-600 text-lg">
          Premium T-Shirts for Champions
        </p>
      </motion.header>

      {/* Cart Summary */}
      <div className="bg-white shadow-md rounded-2xl p-4 mb-8">
        <h2 className="text-2xl font-semibold mb-2">Shopping Cart</h2>
        {cartItems.length === 0 ? (
          <p className="text-gray-500">Your cart is empty.</p>
        ) : (
          <ul className="mb-2">
            {cartItems.map((item) => (
              <li key={item.id} className="flex justify-between items-center text-gray-700 mb-1">
                <span>
                  {item.name} - {item.price} x {item.quantity}
                </span>
                <div className="flex items-center gap-2">
                  <Button size="sm" onClick={() => changeQuantity(item.id, -1)}>-</Button>
                  <Button size="sm" onClick={() => changeQuantity(item.id, 1)}>+</Button>
                  <Button size="sm" variant="destructive" onClick={() => removeFromCart(item.id)}>Remove</Button>
                </div>
              </li>
            ))}
          </ul>
        )}
        <p className="text-blue-800 font-bold mb-2">
          Total: ${total.toFixed(2)}
        </p>
        <Button className="w-full" onClick={handleCheckout}>
          Proceed to Checkout
        </Button>
        {checkoutMessage && (
          <p className="text-green-600 mt-3 font-medium">{checkoutMessage}</p>
        )}
      </div>

      {/* Filters */}
      <div className="mb-6 flex flex-col md:flex-row justify-center items-center gap-4">
        <Input
          placeholder="Search tees..."
          className="w-full max-w-md rounded-2xl shadow-md"
        />

        <select
          value={selectedSize}
          onChange={(e) => setSelectedSize(e.target.value)}
          className="border rounded-2xl shadow-md px-4 py-2 text-gray-700"
        >
          <option value="">All Sizes</option>
          <option value="S">S</option>
          <option value="M">M</option>
          <option value="L">L</option>
          <option value="XL">XL</option>
        </select>
      </div>

      {/* Product Grid */}
      <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-6">
        {filteredProducts.map((product, index) => (
          <motion.div
            key={index}
            initial={{ opacity: 0, scale: 0.9 }}
            animate={{ opacity: 1, scale: 1 }}
            transition={{ duration: 0.4, delay: index * 0.1 }}
          >
            <Card className="rounded-2xl shadow-lg">
              <img
                src={product.image}
                alt={product.name}
                className="w-full h-48 object-cover rounded-t-2xl"
              />
              <CardContent className="p-4">
                <h2 className="text-xl font-semibold mb-1">
                  {product.name}
                </h2>
                <p className="text-blue-700 font-bold mb-2">
                  {product.price}
                </p>
                <p className="text-gray-600 text-sm mb-2">
                  {product.description}
                </p>
                <p className="text-sm text-gray-700 mb-3">
                  Sizes: {product.sizes.join(", ")}
                </p>
                <Button className="w-full" onClick={() => addToCart(product)}>
                  Add to Cart
                </Button>
              </CardContent>
            </Card>
          </motion.div>
        ))}
      </div>
    </div>
  );
}

