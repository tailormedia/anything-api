# AnythingAPI

Need an API that doesn't exist? AnythingAPI turns anything into a structured API. Powered by AI Agents.

> We are currently in closed beta.
> [Join our Discord](https://tally.so/r/w8j1g5) to get early access

## Quickstart

```python
from anythingapi import AnythingAPI

api = AnythingAPI.create(
    from_template="reserve_restaurant_table"
)
api_task = api.post({
  "inputs" : {
    "restaurant_name": "The Restaurant",
    "restaurant_address": "123 Main St, Anytown, USA",
    "booking_date": "2024-12-31",
    "booking_time": "18:00",
    "number_of_people": 4
  }
})

print(api_task.status())

await api_task.wait()

print(api_task.results)
# {
#     "status": "completed",
#     "results": [
#         {
#             "confirmation_number": "1234567890"
#         }
#     ]
# }
```

## Why AnythingAPI?

Not all websites, services and businesses have APIs, and just rely on websites and manual data entry. AnythingAPI turns any website into a structured API using A.I. agents.

Some of the use cases are:

- 🚀 Travel: Reserve at restaurants and/or hotels
- 🛍️ E-commerce: Get product details, prices and reviews
- 📄 Operations: Submit documents to institution websites
- 📡 Monitoring: Gather data from unstructured websites on a regular basis

## Installation

```bash
pip install anythingapi
```

## Usage

### Step 1: Create Your API

You have multiple options to create your API. You can use a pre-defined template (see [templates](#templates)) or define your own.

#### Define your own API with our Web UI

You can define your own API using our Web UI. This is the easiest way to get started and the most flexible, as it allows you to define complex tasks and iterate on them until you get it just right.

> Our WebUI is currently in closed beta.
> [Join our Discord](https://tally.so/r/w8j1g5) to get early access

#### Define your own API with our API

Define your API using a JSON format. This includes the API's task description, input parameters, and expected outputs schema.
The inputs are optional fields that allow you to provide additional context or parameters for the task.
The outputs schema is a JSON schema that defines the expected output format when receiving a response from the API.

```python
from anythingapi import AnythingAPI

api = AnythingAPI.create(
    instructions="Search for hotels in Namibia. For each hotel, find their email address and phone number. If you can't find an email or phone number but their website contains a contact form, fill it up; otherwise, send them an email. In particular, ask them if they have any special offers for the upcoming season. Include the subject 'Special Offer Inquiry' in the email or form. Present yourself as an employee of the company 'Example Inc.' who is researching offers for a corporate event.",
    inputs=[
        {
            "sender_email": string,
            "description": "The email address of the sender, to use when sending emails to the hotels",
        }
    ],
    outputs_schema={
        "type": "object",
        "properties": {
            "hotel_name": {
                "type": "string",
                "description": "The name of the hotel"
            },
            "hotel_email": {
                "type": "string",
                "description": "The email address of the hotel"
            },
            "hotel_phone": {
                "type": "string",
                "description": "The phone number of the hotel"
            },
            "hotel_special_offer": {
                "type": "string",
                "description": "The special offer of the hotel"
            }
        }
    },
    webhook_url="https://yourwebsite.com/webhook"
)

api_id = api.id
```

#### Use a Template

Instead of defining the task manually, you can use one of our predefined [templates](#templates).
Just pass the template name to the define_task method.

```python
api = AnythingAPI.create(
    template="hotel_inquiry"
)
```

## Step 2: Use the API

Once your API is created, AnythingAPI will automatically handle the heavy lifting of crawling, communicating, and coordinating to deliver structured results. You get a standard JSON API that you can use in your project.

### Start a task - POST /tasks

Start the task to initiate the crawling and processing.

```python

api = AnythingAPI.from_id("the_id_you_get_from_step_1")
api_task = api.post({
    "sender_email": "example@example.com"
})
task_id = api_task.id
```

CURL example:

```http
curl -X POST https://api.anythingapi.com/v1/tasks/{task_id} \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"sender_email": "example@example.com"}'
```

Response:

```json
{
  "id": "task_id",
  "status": "running",
  "results": []
}
```

### Monitor the Task - GET /tasks/{task_id}

Monitor the task status and results using the task ID.

```python
status = api_task.status()
```

CURL example:

```http
curl -X GET https://api.anythingapi.com/v1/tasks/{task_id} \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Response:

```json
{
  "id": "task_id",
  "status": "completed",
  "results": [
    {
      "hotel_name": "Hotel A",
      "hotel_email": "hotel@example.com",
      "hotel_phone": "+123456789",
      "hotel_special_offer": "20% off on all rooms"
    }
  ],
  "url": "https://anythingapi.com/tasks/{task_id}"
}
```

### Stop the Task - DELETE /tasks/{task_id}

Stop the task if needed.

```python
api_task.stop()
```

CURL example:

```http
curl -X DELETE https://api.anythingapi.com/v1/tasks/{task_id} \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### List all tasks - GET /tasks

List all tasks you have created.

```python
tasks = AnythingAPI.list_tasks()
```

CURL example:

```http
curl -X GET https://api.anythingapi.com/v1/tasks \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Response:

```json
[
  {
    "id": "task_id",
    "status": "completed"
  }
]
```

### List all APIs - GET /apis

List all APIs you have created in your account.

```python
apis = AnythingAPI.list_apis()
```

CURL example:

```http
curl -X GET https://api.anythingapi.com/v1/apis \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Response:

```json
[
  {
    "id": "api_id",
    "name": "api_name"
  }
]
```

## Templates

AnythingAPI comes with a set of pre-defined templates that you can use to get started:

- [Hotel_inquiry](#hotel_inquiry) - Search for hotels in a region, find their email address and phone number, and send them an email with a special offer
- [Reserve_restaurant_table](#reserve_restaurant_table) - Reserve a table at a restaurant
- [Monitor_website](#monitor_website) - Monitor a regional institution website and send alerts when new data is available

#### License

MIT License
