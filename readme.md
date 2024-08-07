# CV Parsing: 
Our CV parsing API achieves state-of-the-art accuarcy in CV parsing. Works on multilingual CVs. Can handle single-column CVs, multi-columns CVs, and more complex CV layouts. Accepts CVs in various formats, such as PDF, word documents, images or plain text. The API can accurately identify and extract all key CV details like personal information, work experience, education, skills, contact details, etc. This streamlines the recruitment process by automating the extraction of candidate data, enabling faster candidate evaluation and efficient talent acquisition. Our API is easy to integrate into existing systems and applications, providing seamless functionality to streamline HR processes and improve productivity.

## Follow these steps to obtain credentials for using an API from RapidAPI and subscribing to it.

#### Step 1: Create a RapidAPI Account
1. **Visit RapidAPI Website**: Go to [RapidAPI](https://rapidapi.com/).
2. **Sign Up/Log In**:
    - If you don't have an account, sign up using your email, Google, or GitHub account.
    - If you already have an account, log in.

#### Step 2: Find the API
1. **Open the API through this link**: https://rapidapi.com/parsing-ai-parsing-ai-default/api/cv-parser

#### Step 3: Subscribe to the API
1. **Pricing**: Review the pricing plans available for the API: https://rapidapi.com/parsing-ai-parsing-ai-default/api/cv-parser/pricing
3. **Subscribe**: Click on the subscription plan that fits your needs. You may need to provide payment information if you choose a paid plan.

#### Step 4: Obtain API Credentials
1. **Navigate to 'Endpoints'**: Once subscribed, go to the 'Endpoints' tab on the API's details page.
2. **Get Credentials**: You will find your unique API key or credentials that you will use to authenticate your requests. This is usually found in the 'Request Headers' or 'Authentication' section.

#### Step 5: Use the API Key
1. **Integrate API Key**: Use the API key in your application by including it in the headers of your API requests. Typically, the API key needs to be passed as follows:
    ```http
    x-rapidapi-key: YOUR_API_KEY
    ```

## Endpoints available for CV parsing:
1. GET `/health` (Requires Authentication): check if API is working.
2. POST `/parse` (Requires Authentication): return a dictionary of the parsed fields of the CV.
You can parse CVs with two different methods: by sending the file as a base64 string or by providing a public download link. We support a wide range of file extensions, including PDF, DOC, DOCX, JPG, JPEG, and PNG.
- **Image: ["png", "jpeg", "jpg"]**:
  ```
        import requests
        import base64
        import os
        import json

        cv_path = "/CV/image/path/.jpg"

        with open(cv_path, 'rb') as image_file:
            base64_cv = base64.b64encode(image_file.read()).decode()

        url = "IP/ADDRESS/parse"

        # Define the payload
        payload = {
            "data_bytes": base64_cv,
            "isbytes": True,
            "file_type" : "jpg" #["png", "jpeg", "jpg"]
        }

        headers = {
        	"x-rapidapi-key": os.getenv("rapid_key"),
        	"x-rapidapi-host": "cv-parser.p.rapidapi.com",
        	"Content-Type": "application/json"
        }

        response = requests.post(url, json=payload, headers=headers)

        print(response.json())
        ```
- **Files: ["pdf", "docx", "doc"]**:
     ```
        import requests
        import base64
        import os

        cv_path = "/CV/PDF/path/.pdf"   #example of pdf extension
        # cv_path = "/CV/Docx/path/.docx" #example of docx extension

        with open(cv_path, 'rb') as f:
            pdf = f.read()

        base64_cv = base64.b64encode(pdf).decode()

        url = "IP/ADDRESS/parse"

        # Define the payload
        payload = {
            "data_bytes": base64_cv,
            "isbytes": True,
            "file_type" : "pdf" #["pdf", "docx", "doc"] pdf by default
        }

        headers = {
        	"x-rapidapi-key": os.getenv("rapid_key"),
        	"x-rapidapi-host": "cv-parser.p.rapidapi.com",
        	"Content-Type": "application/json"
        }

        response = requests.post(url, json=payload, headers=headers)

        print(response.json())
        ```  
- **URL: ["pdf", "docx", "doc","png", "jpeg", "jpg"]**:
    ```
        import requests
        import json
        import os


                
        drive_id = "1M..."
        # Steps to get the drive id: 
        # 1- upload the file on the drive 
        # 2- open the file and press on share
        # 3- Make the general access viewer or editor to anyone with the link 
        # 4- copy the link and you can find the id here: https://drive.google.com/file/d/1M..../view..
        
        
        url = "https://cv-parser.p.rapidapi.com/parse"
        
        payload = {
        	#the download link could be for any file ["pdf", "docx", "doc","png", "jpeg", "jpg"]
        
        
         # normal download link from drive
        	"file_url": f"https://drive.google.com/uc?export=download&id={drive_id}",
        
        	 #export link for docx file as pdf
        	# "file_url": f"https://docs.google.com/document/d/{drive_id}/export?format=pdf",
        	# Note: you can parse any public file with public links including s3 links
         
        	"isbytes": False,
        	"file_type" : "pdf" #["pdf", "docx", "doc","png", "jpeg", "jpg"] pdf by default
        }
        headers = {
          "x-rapidapi-key": os.getenv("rapid_key"),
          "x-rapidapi-host": "cv-parser.p.rapidapi.com",
          "Content-Type": "application/json"
        }

        response = requests.post(url, json=payload, headers=headers)

        print(response.json())


        ```  

**Note that there is a limit for number of cv pages by 5, the user is responsible for the corruption if pages exceed the limit**
 

- **response (NOTE: a field is "None" if not found):**
  - `success (bool)`:
    - `True` if operations are done without error
    - `False` if an error is encountered
  - `msg (string)`: message showing the error in case of error
    - Empty string in case of success
  - `cv_parsing (dictionary)`: The output in case of a successful operation, empty dict in case of error
 
      - `full_name (string)`: Name of the applicant
      - `contact_information (dictionary)`:
        - `email_address (list of strings)`
        - `phone_numbers (list of strings)`
        - `address (dictionary)`:
          - `full_address (string)`
          - `country (string)`
          - `state/governate (string)`
          - `city/town (string)`
      - `linkedin_url (string)`
      - `personal_websites (list of string)`
      - `career_objective (string)`
      - `professional_summary (string)`
      - `profession (string)`: CV title or last job
      - `education (list of dictionaries)`:
        - Each dictionary contains:
          - `institution_name (string)`
          - `degree (string)`: Associates Degree
          - `major (string)`: Specialization
          - `grade (string)`: GPA score, percentage or excellent, v.good, good
          - `start_date (date-time format: YYYY-MM-DD)`
          - `graduation_date (date-time format: YYYY-MM-DD)`
          - `duration (dictionary)`:
            - `years (integer)`
            - `months (integer)`
          - `description (string)`
          - `location (string)`
      - `skills (dictionary)`:
        - `technical_skills (list of strings)`
        - `soft_skills (list of strings)`
        - `inferred_skills (list of strings)`: these are the skills which not mentioned directly as skills in the CV.
      - `projects (list of dictionaries)`:
        - Each dictionary contains:
          - `project_title (string)`
          - `project_type (string)`: educational, professional, personal
          - `description (string)`
      - `publications (list of strings)`
      - `certifications_and_licenses (list of strings)`
      - `languages (list of dictionaries)`:
        - Each dictionary contains:
          - `language (string)`
          - `level (string)`
      - `honors_and_awards (list of strings)`
      - `conferences_and_workshops (list of strings)`
      - `volunteer_experience (list of dictionaries)`:
        - Each dictionary contains:
          - `organization_name (string)`
          - `role (string)`
          - `start_date (date-time format: YYYY-MM-DD)`
          - `end_date (date-time format: YYYY-MM-DD)`
          - `duration (dictionary)`:
            - `years (integer)`
            - `months (integer)`
          - `description (string)`
      - `hobbies_and_interests (list of strings)`
      - `references (list of dictionaries)`:
        - Each dictionary contains:
          - `name (string)`
          - `position (string)`
          - `company (string)`
          - `contact_information (dictionary)`:
            - `email_address (string)`
            - `phone_number (string)`
      - `parsing_date (string)`
      - `CV_source (link)`

        
Example on payload and response using dummy CV: 
```
{
    "file_link": "https://drive.google.com/uc?export=download&id=1DgcrBrgo6ApIkkudSDTOaXwI6UUJMsbX",
    "data_bytes": "",
    "isbytes": false,
    "file_type" : "pdf"
}
```
- Response
```
{
  "success": true,
  "cv_parsing": {
    "full_name": "Jason Miller",
    "contact_information": {
      "email_address": ["email@email.com"],
      "phone_numbers": ["3868683442"],
      "address": {
        "full_address": "1515 Pacific Ave, Los Angeles, CA 90291",
        "country": "United States",
        "state/governate": "California",
        "city/town": "Los Angeles"
      }
    },
    "linkedin_url": "https://www.linkedin.com/",
    "personal_websites": [
      "https://www.pinterest.es/resumeviking/",
      "https://www.resumeviking.com/templates/",
      "https://resume.io/?id=cpdokyF1"
    ],
    "career_objective": null,
    "professional_summary": "Experienced Amazon Associate with five years’ tenure in a shipping yard setting, maintaining an average picking/packing speed of 98%. Holds a zero error% score in adhering to packing specs and 97% error-free ratio on packing records. Completed a certificate in Warehouse Sanitation and has a valid commercial driver’s license.",
    "profession": "Amazon Associate",
    "education": [
      {
        "institution_name": "Atlanta Technical College",
        "degree": "Associates Degree",
        "major": "Logistics and Supply Chain Fundamentals",
        "grade": null,
        "start_date": null,
        "graduation_date": null,
        "duration": {
          "years": 1,
          "months": 6
        },
        "honors_and_awards": [
          "Employee of the month due to consistent attendance, punctuality, and performance."
        ]
      }
    ],
    "work_experience": [
      {
        "organization_name": "Amazon",
        "job_title": "Warehouse Associate",
        "start_date": null,
        "end_date": null,
        "duration": {
          "years": 1,
          "months": 6
        },
        "description": "Performed all warehouse laborer duties such as packing, picking, counting, record keeping, and maintaining a clean area. Consistently maintained picking/packing speeds in the 98th percentile. Picked all orders with 100% accuracy despite high speeds. Maintained a clean work area, meeting 97.5% of the inspection requirements.",
        "location": "Miami Gardens"
      },
      {
        "organization_name": "Dunrea Laboratories",
        "job_title": "Laboratory Inventory Assistant",
        "start_date": null,
        "end_date": null,
        "duration": {
          "years": 1,
          "months": 12
        },
        "description": "Full-time lab assistant in a small, regional laboratory tasked with participating in Kaizen Events, Gemba walks, and 5S to remove barriers and improve productivity. Filled the warehouse helper job description, which involved picking, packing, shipping, inventory management, and cleaning equipment. Saved 12% on UPS orders by staying on top of special deals. Cut down storage waste by 23% by switching to a Kanban system.",
        "location": "Orlando"
      }
    ],
    "internships": [],
    "skills": {
      "technical_skills": [
        "Cleaning Equipment",
        "Mathematics",
        "Deep Sanitation Practices"
      ],
      "soft_skills": []
    },
    "inferred_skills": [
      "Warehouse Operations",
      "Inventory Management",
      "Shipping and Receiving"
    ],
    "projects": [],
    "publications": [],
    "certifications_and_licenses": [
      "Certificate in Warehouse Sanitation",
      "Valid Commercial Driver's License"
    ],
    "languages": [
      {
        "language": "English",
        "level": "Fluent"
      },
      {
        "language": "Spanish",
        "level": "Conversational"
      }
    ],
    "honors_and_awards": [
      "Employee of the month due to consistent attendance, punctuality, and performance."
    ],
    "conferences_and_workshops": [],
    "volunteer_experience": [],
    "hobbies_and_interests": [
      "Action Cricket",
      "Rugby",
      "Athletics"
    ],
    "courses": [
      {
        "course_name": "Online Graduate Certificate in Warehousing & Supply Chain Management",
        "description": "Southern New Hampshire University (SNHU), NH."
      },
      {
        "course_name": "Warehousing, Operations, and Disposal Course",
        "description": "Graduate School USA, Washington DC."
      }
    ],
    "references": [],
    "parsing_date": "2024-08-05",
    "CV_source": "link"
  },
  "msg": ""
}

```
