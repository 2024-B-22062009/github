# wastemanagement

from flask import Flask, request, jsonify
import uuid

app = Flask(__name__)

# In-memory storage
collection_requests = []

# Waste categories
valid_categories = ["organic", "recyclable", "hazardous", "others"]

# Request statuses
statuses = ["pending", "collected"]

# 📦 Create a new collection request
@app.route("/request-collection", methods=["POST"])
def request_collection():
    data = request.get_json()
    name = data.get("name")
    category = data.get("category", "others").lower()
    location = data.get("location")
    quantity = data.get("quantity", 1)

    if not all([name, location]):
        return jsonify({"error": "Missing required fields: name or location"}), 400

    if category not in valid_categories:
        category = "others"

    request_id = str(uuid.uuid4())
    collection = {
        "id": request_id,
        "name": name,
        "category": category,
        "location": location,
        "quantity": quantity,
        "status": "pending"
    }

    collection_requests.append(collection)
    return jsonify({"message": "Collection request submitted", "id": request_id}), 201

# 📋 View all collection requests
@app.route("/collection-requests", methods=["GET"])
def get_all_requests():
    return jsonify(collection_requests), 200

# 🔁 Update collection status
@app.route("/update-status/<request_id>", methods=["PATCH"])
def update_status(request_id):
    data = request.get_json()
    new_status = data.get("status", "").lower()

    if new_status not in statuses:
        return jsonify({"error": "Invalid status. Use 'pending' or 'collected'."}), 400

    for request_obj in collection_requests:
        if request_obj["id"] == request_id:
            request_obj["status"] = new_status
            return jsonify({"message": "Status updated"}), 200

    return jsonify({"error": "Request not found"}), 404

# 🏠 Welcome route
@app.route("/")
def home():
    return jsonify({"message": "Waste Collection Management API is running"}), 200

if __name__ == "__main__":
    app.run(debug=True)
