
[Generating prompt](https://chatgpt.com/share/672b8163-25c0-8003-8423-1941ef57ca80)

# EIP Title: JSON Smart Contract with Value Version Control

**EIP Number:** (Assign upon submission)

**Type:** Standard Track

**Category:** Interface

**Status:** Draft

**Created:** November 7, 2024  

## Abstract

This EIP defines a smart contract interface that allows for managing a JSON object within a smart contract, offering both real-time JSON output and a version-controlled history for each value. 
The interface includes methods to retrieve the most recent JSON state as well as the version history of each key in the JSON object. 
This approach supports REST developers familiar with JSON-based data interactions, thus improving accessibility for developers new to Web3 and Ethereum.

## Motivation

With an increasing number of developers from RESTful backgrounds joining the Ethereum ecosystem, there is a need for a contract interface that allows developers to easily interact with structured JSON data. 
This EIP aims to create a universal standard that provides JSON data management and version control functionality in a straightforward and REST-like way, making Ethereum more accessible and developer-friendly.

## Specification

The contract interface includes the following methods:

### Read Methods

1. **`json()`**

   - **Inputs:** None.
   - **Output:** `string` (a JSON object as a string)
   - **Description:** Returns the JSON object in its latest state. The output uses the most recent value for each key in the JSON object, mimicking a REST JSON call.
   - **Behavior:** Each key in the JSON object reflects the latest "Last In, First Out" (LIFO) state of its value.

2. **`version(string key)`**

   - **Inputs:** 
     - `key` (`string`): The JSON key whose version history is requested.
   - **Output:** `string` (a JSON array as a string)
   - **Description:** Returns an array of all versions of the specified key's value in JSON format. 
     The array is ordered chronologically, with the earliest version at index 0 and the most recent version at the highest index.

### Write Method

1. **`write(string[] keys, string[] values, bool replace)`**

   - **Inputs:**
     - `keys` (`string[]`): Array of JSON key strings, where each key represents a path within the JSON object.
     - `values` (`string[]`): Array of JSON-compatible strings corresponding to each key. Can represent primitive types, arrays, or objects.
     - `replace` (`bool`): Determines whether to replace an existing key's latest value.
       - If `replace` is `true` and the key exists, the existing value is updated in the `json` output.
       - However, the previous value persists in the `version` output, with the new value appended at the end of the version history array.
       - If `replace` is `false` and the key already exists, the transaction reverts.
   - **Output:** None.
   - **Description:** Writes new values to the JSON object, either adding a new key or updating an existing one, based on the `replace` parameter.

#### Additional Requirements

- **Universal ABI:** To support widespread adoption and ease of use, the ABI should be standardized for REST-like abstraction within popular Web3 libraries. 
  By making this contract accessible through Web3 abstractions, developers with REST experience can seamlessly interact with the contract.
  
## Rationale

1. **REST-like Access via JSON Method**  
   The `json` method enables developers to interact with the contract as if it were a RESTful API, improving accessibility for those familiar with traditional web development paradigms.

2. **Version Management via Version Method**  
   The `version` method provides a straightforward version control system for each key, offering a history of values that developers can reference without altering the main JSON structure. 
   This method maintains immutability for historical values while allowing updates to be appended.

3. **Compatibility with Web3 Abstractions**  
   Ensuring a simple and standardized ABI is essential for usability with Web3 libraries, thus enhancing developer experience and facilitating onboarding.

## Example Usage

Assume the following scenario with key-value management:

1. **Initial Write:**  
   ```solidity
   write(["name"], ["Alice"], false);
   ```
   - JSON Output after this call:
     ```json
     { "name": "Alice" }
     ```
   - Version History of `name`:
     ```json
     ["Alice"]
     ```

2. **Updating Value with Replacement:**  
   ```solidity
   write(["name"], ["Bob"], true);
   ```
   - JSON Output after this call:
     ```json
     { "name": "Bob" }
     ```
   - Version History of `name`:
     ```json
     ["Alice", "Bob"]
     ```

3. **Attempting to Update without Replacement (Reverts if `name` exists):**  
   ```solidity
   write(["name"], ["Charlie"], false);
   ```
   - This transaction would revert as `name` already exists, and `replace` is set to `false`.

## Backwards Compatibility

This EIP is a new standard and does not interfere with existing standards. 
However, it introduces JSON object handling and version control, which may have specific considerations for gas optimization.

## Security Considerations

Care should be taken to handle large JSON objects efficiently to avoid excessive gas consumption.
