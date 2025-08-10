# gemini-cli custom tools

These settings are in the .gemini/settings.json file.

toolDiscoveryCommand (string):

Description: Defines a custom shell command for discovering tools from your project. The shell command must return on stdout a JSON array of function declarations. Tool wrappers are optional.
Default: Empty
Example: "toolDiscoveryCommand": "bin/get_tools"

toolCallCommand (string):

Description: Defines a custom shell command for calling a specific tool that was discovered using toolDiscoveryCommand. The shell command must meet the following criteria:
It must take function name (exactly as in function declaration) as first command line argument.
It must read function arguments as JSON on stdin, analogous to functionCall.args.
It must return function output as JSON on stdout, analogous to functionResponse.response.content.
Default: Empty
Example: "toolCallCommand": "bin/call_tool"

## Function Declarations

When you implement function calling in a prompt, you create a tools object, which contains one or more function declarations. You define functions using JSON, specifically with a select subset of the OpenAPI schema format. A single function declaration can include the following parameters:

name (string): A unique name for the function (get_weather_forecast, send_email). Use descriptive names without spaces or special characters (use underscores or camelCase).
description (string): A clear and detailed explanation of the function's purpose and capabilities. This is crucial for the model to understand when to use the function. Be specific and provide examples if helpful ("Finds theaters based on location and optionally movie title which is currently playing in theaters.").
parameters (object): Defines the input parameters the function expects.
type (string): Specifies the overall data type, such as object.
properties (object): Lists individual parameters, each with:
type (string): The data type of the parameter, such as string, integer, boolean, array.
description (string): A description of the parameter's purpose and format. Provide examples and constraints ("The city and state, e.g., 'San Francisco, CA' or a zip code e.g., '95616'.").
enum (array, optional): If the parameter values are from a fixed set, use "enum" to list the allowed values instead of just describing them in the description. This improves accuracy ("enum": ["daylight", "cool", "warm"]).
required (array): An array of strings listing the parameter names that are mandatory for the function to operate.
You can also construct FunctionDeclarations from Python functions directly using types.FunctionDeclaration.from_callable(client=client, callable=your_function).

## functionCall.args

A predicted functionCall returned from the model that contains a string representing the functionDeclaration.name and a structured JSON object containing the parameters and their values.

Parameters
name

string

The name of the function to call.

args

Struct

The function parameters and values in JSON object format.

See Function calling for parameter details.

## functionResponse.response.content

The resulting output from a FunctionCall that contains a string representing the FunctionDeclaration.name. Also contains a structured JSON object with the output from the function (and uses it as context for the model). This should contain the result of a FunctionCall made based on model prediction.

Parameters
name

string

The name of the function to call.

response

Struct

The function response in JSON object format.
