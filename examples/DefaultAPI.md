HOST: http://www.google.com/
Format: 1A

# Sample API v2
Welcome to the our sample API documentation. All comments can be written in (support [Markdown](http://daringfireball.net/projects/markdown/syntax) syntax).

# Shopping Cart Resources
The following is a section of resources related to the shopping cart.

## GET /shopping-cart
List products added into your shopping-cart. (comment block again in Markdown)
### Response 200 (application/json)
	{ "items": [
	  { "url": "/shopping-cart/1", "product":"2ZY48XPZ", "quantity": 1, "name": "New socks", "price": 1.25 }
	] }

##  POST /shopping-cart
Saves new products in your shopping cart.
### Request (application/json)
	{ "product":"1AB23ORM", "quantity": 2 }
### Response 201 (application/json)
	{ "status": "created", "url": "/shopping-cart/2" }

# Payment Resources

## POST /payment
This resource allows you to submit payment information to process your *shopping cart* items.
### Request
	{ "cc": "12345678900", "cvc": "123", "expiry": "0112" }
### Response 200 (application/json)
	{ "receipt": "/payment/receipt/1" }
