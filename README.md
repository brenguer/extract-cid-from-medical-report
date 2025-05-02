This Jupyter Notebook automates the extraction and validation of **ICD-10 codes (CIDs)** from medical reports (PCD laudos) stored in URLs within a Google Sheet. It leverages **Google Workspace tools (Colab, Sheets, Drive)** and the **Gemini API** to process documents, validate data, and update results in real time. Below is a breakdown of its core functionalities:

## **Key Features**
1. **Google Workspace Integration**  
   - Authenticates with Google Sheets and Drive APIs.  
   - Reads input data from a specified Google Sheet (`gemini2` worksheet).  
   - Updates processed results back to the same sheet automatically.  

2. **ICD Code Extraction with Gemini**  
   - Processes URLs pointing to medical documents (PDFs, JPEGs, etc.).  
   - Uses the **Gemini 1.5 Flash model** to:  
     - Extract ICD codes (e.g., `F84`, `F32.1`) from unstructured text.  
     - Identify report metadata (date, physician CRM, report type: *digitado*/*mão*).  
   - Handles OCR errors and fallback regex extraction if Gemini parsing fails.  

3. **Validation & Cross-Checking**  
   - Validates extracted ICDs against:  
     - A predefined list of PCD-eligible ICDs (from the `cid_data` worksheet).  
     - User-inputted ICDs (`cid_candidato` column).  
   - Flags mismatches and invalid codes.  
   - Adds descriptions for ICDs using a reference CID dictionary.  

4. **Automated Workflow**  
   - Downloads and processes files via `googleapiclient` and `httpx`.  
   - Implements rate limiting (5-7s delays) to avoid API throttling.  
   - Logs errors and skips invalid URLs gracefully.  

---

## **Data Flow**
1. **Input**:  
   - Google Sheet with URLs to medical reports and user-provided ICD guesses.  
   - Reference CID list (`cid_data` sheet) for PCD validation.  

2. **Processing Steps**:  
   - **Gemini Analysis**: Extracts ICDs, dates, and CRM numbers from documents.  
   - **CID Validation**: Checks if extracted ICDs match user inputs and PCD eligibility.  
   - **Description Mapping**: Adds human-readable descriptions to ICDs using a CID dictionary.  

3. **Output**:  
   - Updated Google Sheet with columns:  
     - `cid_extract-1`, `cid_extract-2`: Extracted ICDs.  
     - `validacao_cruzada`: Validation status (TRUE/FALSE).  
     - `enquadramento`: PCD eligibility flag.  
     - `cid_candidato_tratado_descr`: ICD descriptions.  

---

## **Tools & Libraries**
- **Google APIs**: `gspread`, `google-auth`, `google-generativeai`.  
- **Data Processing**: `pandas`, `numpy`, `re`.  
- **Document Handling**: `httpx`, `googleapiclient.http.MediaIoBaseDownload`.  
- **Logging & Delays**: `logging`, `time`, `datetime`.  

---

## **Use Cases**
- **Healthcare Compliance**: Verify if medical reports align with PCD regulations.  
- **Data Auditing**: Cross-check user-provided ICDs against automated extractions.  
- **Report Standardization**: Ensure consistency in medical documentation.  
