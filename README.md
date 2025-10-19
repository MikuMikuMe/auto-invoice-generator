# auto-invoice-generator

Creating an auto-invoice generator involves several key steps: designing a customizable invoice template, gathering the necessary invoice data, and integrating with accounting software for seamless management. Below is a basic Python program to achieve these functionalities. This script will create invoices dynamically and save them as PDFs with the option to extend functionality for integration with popular accounting software like QuickBooks or Xero.

For the purpose of this example, let's use `reportlab` to generate PDFs and include some basic error handling and comments for clarity.

```python
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas
from reportlab.lib import colors
import os

# Constants for the PDF
INVOICE_TEMPLATE_PATH = "invoice_template.pdf"
OUTPUT_PATH = "generated_invoices/"
LOG_FILE = "error_log.txt"

def create_invoice(invoice_data):
    """Generate an invoice PDF with given invoice data."""
    try:
        os.makedirs(OUTPUT_PATH, exist_ok=True)
        
        file_path = os.path.join(OUTPUT_PATH, f"invoice_{invoice_data['invoice_number']}.pdf")
        c = canvas.Canvas(file_path, pagesize=letter)

        # Create a standard format for the invoice
        c.setFont("Helvetica", 12)
        c.drawString(100, 750, "Invoice")
        c.drawString(450, 750, f"Invoice #: {invoice_data['invoice_number']}")
        c.drawString(100, 730, f"Date: {invoice_data['date']}")
        
        # Client Information
        c.drawString(100, 710, f"Bill To: {invoice_data['client_name']}")
        c.drawString(100, 690, invoice_data['client_address'])
        
        # Business Information
        c.drawString(100, 670, "From:")
        c.drawString(100, 650, invoice_data['business_name'])
        c.drawString(100, 630, invoice_data['business_address'])
        
        # Table for items
        c.drawString(100, 600, "Description")
        c.drawString(300, 600, "Quantity")
        c.drawString(400, 600, "Price")
        c.drawString(500, 600, "Total")
        c.line(100, 595, 550, 595)
        
        y_position = 575
        for item in invoice_data['items']:
            c.drawString(100, y_position, item['description'])
            c.drawString(300, y_position, str(item['quantity']))
            c.drawString(400, y_position, f"${item['price']:.2f}")
            c.drawString(500, y_position, f"${item['total']:.2f}")
            y_position -= 20
        
        # Total
        c.drawString(400, y_position - 20, "Subtotal:")
        c.drawString(500, y_position - 20, f"${invoice_data['subtotal']:.2f}")

        c.drawString(400, y_position - 40, "Tax:")
        c.drawString(500, y_position - 40, f"${invoice_data['tax']:.2f}")

        c.line(400, y_position - 45, 550, y_position - 45)

        c.drawString(400, y_position - 60, "Total:")
        c.setFont("Helvetica-Bold", 12)
        c.drawString(500, y_position - 60, f"${invoice_data['total']:.2f}")
        
        c.save()
        print(f"Invoice {invoice_data['invoice_number']} successfully generated.")
    except Exception as e:
        with open(LOG_FILE, "a") as logfile:
            logfile.write(f"Error creating invoice {invoice_data['invoice_number']}: {e}\n")
        print(f"Failed to create invoice {invoice_data['invoice_number']}. Error logged.")

def main():
    # Sample data to demonstrate
    invoice_data = {
        'invoice_number': '0001',
        'date': '2023-10-15',
        'client_name': 'Acme Corp.',
        'client_address': '123 Business St, Commerce City, Commerce State',
        'business_name': 'Freelance Solutions',
        'business_address': '987 Independent Ave, Entrepreneur City, Freelance State',
        'items': [
            {'description': 'Web Development', 'quantity': 30, 'price': 50.00, 'total': 1500.00},
            {'description': 'Graphic Design', 'quantity': 10, 'price': 40.00, 'total': 400.00},
        ],
        'subtotal': 1900.00,
        'tax': 190.00,
        'total': 2090.00
    }
    
    create_invoice(invoice_data)

if __name__ == "__main__":
    main()
```

### Key Points:
- **Error Handling**: The code includes basic error handling that logs errors during the invoice creation process.
- **External Libraries**: It uses the `reportlab` library to generate PDF files.
- **Directory Management**: Ensures the output directory exists before saving invoices.
- **Customization**: You can expand the `invoice_data` dictionary for more customization options, such as different tax rates or additional item details.
- **Integration**: This example doesn't include integration with accounting software due to the complexity and need for specific APIs or SDKs. To integrate, you would typically use an API client library provided by the accounting software.

To fully integrate with an accounting platform, consult the API documentation of the platform you intend to use and expand the program accordingly.