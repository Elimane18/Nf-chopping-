# Nf-chopping-
Vente d’habillement la maison de la mode 
from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy
import stripe
app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///shop.db'
db = SQLAlchemy(app)
stripe.api_key = "VOTRE_CLE_STRIPE_SECRETE"
class Product(db.Model):
id = db.Column(db.Integer, primary_key=True)
name = db.Column(db.String(100))
price = db.Column(db.Float)
class Order(db.Model):
id = db.Column(db.Integer, primary_key=True)
total = db.Column(db.Float)
paid = db.Column(db.Boolean, default=False)
@app.route("/")
def index():
products = Product.query.all()
return render_template("index.html", products=products)
@app.route("/checkout", methods=["POST"])
def checkout():
total_amount = float(request.form["total"])
intent = stripe.PaymentIntent.create(
amount=int(total_amount * 100),
currency="eur",
payment_method_types=["card"],
)
order = Order(total=total_amount, paid=True)
db.session.add(order)
db.session.commit()
return "Paiement réussi. Argent stocké sur votre compte Stripe."
if __name__ == "__main__":
db.create_all()
app.run(debug=True)