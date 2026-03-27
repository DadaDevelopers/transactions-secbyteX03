# Bitcoin Transaction Decoder Assignment Report

## Assignment Overview

This assignment focuses on manually decoding a raw Bitcoin transaction hex and building a Python program that can automatically decode Bitcoin transactions.

The transaction used in this assignment is a **SegWit transaction**, meaning it includes a marker, flag, inputs, outputs, witness data, and locktime.

The main goal of this project was to understand how Bitcoin transactions are structured at the byte level and how they can be converted into a readable and meaningful format.


 ## Files Included in This Assignment
 - `manual-decode.md` -  contains the step-by-step manual decoding of the transaction
- `decoder.py`-  Python script used to automatically decode the transaction
- `output.txt` - output generated after running the decoder
- `README.md` - explanation of the project and implementation
---

## What I Did
 ### Manual Transaction Decoding 
I manually decoded the transaction hex by breaking it down into its core components:
 - version
- marker and flag
- number of inputs
- input details
- number of outputs
- output details
- witness data
- locktime 
This process helped me understand how Bitcoin transactions are structured internally and how SegWit transactions differ from legacy transactions.
 --- 

### Python Transaction Decoder
 I developed a Python function called `decode_transaction(hex_string)` that:
 - accepts a Bitcoin transaction in hexadecimal format
- converts the hex string into raw bytes
- reads each part of the transaction in the correct order
- handles little-endian byte ordering correctly
- detects whether the transaction is a SegWit transaction
- extracts inputs, outputs, witness data, and locktime
- returns the decoded transaction as a Python dictionary
 --- 

## How the Code Works 
### `read_varint(data, offset)` 
This function reads VarInt values used in Bitcoin transactions.
 Bitcoin uses VarInt to store variable-length integers. The function checks the first byte and determines how many additional bytes should be read:
 - if the value is less than 253, it is stored in a single byte
- if the value is 253, the next 2 bytes are read
- if the value is 254, the next 4 bytes are read
- if the value is 255, the next 8 bytes are read 
This is used to determine sizes such as the number of inputs, outputs, and script lengths.
 ---

 ### `decode_transaction(hex_string)` 
The main decoding function follows these steps:

1. **Convert** hex string to raw bytes
2. **Read version** (first 4 bytes, little-endian)
3. **Check SegWit** - if marker `00` and flag `01`, transaction is SegWit
4. **Parse inputs** - extract for each input:
   - Previous transaction hash which is reversed for little-endian
   - Previous output index
   - Script length and signature
   - Sequence number
5. **Parse outputs** - extract for each output:
   - Amount in satoshis
   - Script length and public key
6. **Parse witness data** (for SegWit transactions)
7. **Read locktime** (final 4 bytes)
 ---

 ## Little-Endian Format 
Bitcoin stores many values in little-endian format, meaning the least significant byte is stored first. Because of this, values such as transaction hashes and numerical fields must be converted from little-endian format to obtain their correct values. 
This applies to:
 - version- transaction hash
- output index
- output amounts
- locktime 
--- 

## Output Formatting 
To improve readability, I formatted the decoded transaction using Python’s `json.dumps()` function. This ensures the output is clean, structured, and easy to read. I used the `indent=2` option to format the output with proper spacing. 
```python
import json
print(json.dumps(decoded, indent=2))

```

## Validation and Output Analysis

After running the decoder, I compared the output against the actual Bitcoin transaction data to verify correctness.

The results confirm that the decoder is functioning accurately:

* The transaction correctly identifies the SegWit structure through the marker and flag values (`00` and `01`).
 
* The input is properly parsed, including the previous transaction hash and output index, with correct handling of little-endian formatting.
 

* The output values are accurate and match real Bitcoin amounts in satoshis.
 
* The scriptPubKey values follow the expected SegWit format, confirming that modern transaction scripts are handled correctly.
 
* The witness data is successfully extracted, including both the signature and the public key. This demonstrates correct parsing of SegWit witness data.
 
* The locktime value matches the expected value from the actual transaction, confirming full correctness of the parsing process.
 

Overall, the decoded output closely matches the real transaction structure found on the Bitcoin blockchain, confirming the correctness of the decoder.

## Test Transaction

I tested the decoder using the provided transaction hex.

The program successfully decoded:

* version
 
* marker and flag
 
* one input
 
* two outputs
 
* witness data
 
* locktime
 

## How to Run the Program
```bash
# Run the decoder
python3 decoder.py

# Save output to file
python3 decoder.py > output.txt
```
## Output

The decoded transaction is printed as a structured Python dictionary in JSON format:

{
  "version": 2,
  "marker": "00",
  "flag": "01",
  "inputs": [...],
  "outputs": [...],
  "witness": [...],
  "locktime": ...
}

#### **Small Observation**

For SegWit transactions, the `scriptSig` field is empty. This is expected because SegWit stores validation data in the witness section instead.

## What I Learned from doing the assignment

Through this assignment, I learned:

* how Bitcoin transaction hex is structured
 
* how SegWit transactions work
 
* how to manually decode transaction data
 
* how to parse binary data in Python
 
* how VarInt encoding works
 
* how little-endian format is used in Bitcoin


