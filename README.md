✅ How to Create a Gradio Bot That Sends Data to Google Sheets
Step 1: Create Your Google Sheet
Go to Google Sheets.

Create a new sheet.

Rename the first sheet tab to something like: Sheet1.

Add column headers (optional):

pgsql
Copy
Edit
Name | Phone Number | Bot Type | Location | Budget | Message | Timestamp
Step 2: Set Up Google Apps Script
Click Extensions → Apps Script.

Delete the default code.

Paste this code:

javascript
Copy
Edit
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Sheet1"); // your sheet name

  var data = JSON.parse(e.postData.contents);

  var name = data.name || "";
  var phone = data.phone_number || "";
  var botType = data.bot_type || "";
  var location = data.location || "";
  var budget = data.budget || "";
  var message = data.message || "";
  var timestamp = new Date();

  sheet.appendRow([name, phone, botType, location, budget, message, timestamp]);

  return ContentService
    .createTextOutput("Form data successfully submitted.")
    .setMimeType(ContentService.MimeType.TEXT);
}
Click the disk icon to save. Name the project (e.g., “Bot Form Handler”).

Step 3: Deploy as Web App
Click Deploy → Manage deployments.

Click + New deployment.

Click the gear icon ⚙ and choose “Web App”.

Fill in:

Description: e.g., “Bot Data Receiver”

Execute as: Me

Who has access: Anyone

Click Deploy.

Copy the Web App URL — you'll need it in your Python script.

Example:

bash
Copy
Edit
https://script.google.com/macros/s/AKfycbw.../exec
Step 4: Install Python and Gradio
If you don’t already have Python and Gradio:

bash
Copy
Edit
pip install gradio requests
Step 5: Create the Gradio Python Script
Paste the following Python code into a .py file (e.g., bot_form.py):

python
Copy
Edit
import gradio as gr
import requests

GOOGLE_SCRIPT_URL = "https://script.google.com/macros/s/PASTE_YOUR_URL_HERE/exec"

def collect_info(name, phone_number, bot_type, location, budget, message):
    data = {
        "name": name,
        "phone_number": phone_number,
        "bot_type": bot_type,
        "location": location,
        "budget": budget,
        "message": message
    }

    try:
        response = requests.post(GOOGLE_SCRIPT_URL, json=data)
        if response.status_code == 200:
            sheet_response = "✅ Data saved to Google Sheet successfully!"
        else:
            sheet_response = f"⚠️ Error saving data: {response.text}"
    except Exception as e:
        sheet_response = f"❌ Request failed: {e}"

    return f"""
Thank you, {name}, for your interest!

Details received:
- Phone Number: {phone_number}
- Bot Type: {bot_type}
- Location: {location}
- Budget: {budget}
- Message: {message}

We will contact you shortly.

If you want to reach us directly:
Name: Abid Ali  
Phone: 0346-4931953  
Email: abidali431992@gmail.com

{sheet_response}
"""

bot_types = ["Clinic Appointment Bot", "Hospital Bot", "School Bot", "Small Business Bot", "Big Business Bot", "Shop Bot", "Other"]
budgets = ["25K (Basic Features)", "30K (Medium Features)", "35K (Advanced)", "40K (Full Features)"]

demo = gr.Interface(
    fn=collect_info,
    inputs=[
        gr.Textbox(label="name"),
        gr.Textbox(label="phone_number"),
        gr.Dropdown(bot_types, label="bot_type"),
        gr.Textbox(label="location"),
        gr.Dropdown(budgets, label="budget"),
        gr.Textbox(label="message (optional)")
    ],
    outputs="text",
    title="Abid Bot Services - Inquiry Form"
)

demo.launch()
✅ Replace the GOOGLE_SCRIPT_URL with your actual Google Apps Script Web App URL.

Step 6: Run Your Bot
bash
Copy
Edit
python bot_form.py
You’ll get a local link to test your bot in your browser.

✅ Optional: Deploy to Hugging Face (Optional)
You can also upload this script to Hugging Face Spaces using Gradio as the SDK to make it public.


