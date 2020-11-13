# Synthetic Function Generator
Synthetic function generator from the manuscript "Sizeless: Predicting the optimal size of serverless functions" 
Er (1) generiert funktionen baseierend auf segmenten (2) er kombiniert sie randomly (3) Monitored zeugs (4) benchmarken
Er erlaubt es viele funtionen zu generieren und benchmarken

TODO Bestehende function segments
TODO Extending function segments

TODO Bestehnde metriken
TODO Metriken extenden

TODO Cites us ähnlich wie bei teastore mit arxiv link (siehe rechts oben)
## Building the CLI

Requirements:

- golang installed (tested for versions 1.14+)

Inside the `function-generator` directory run the command `go build .`.
By default this results in a file named `synthetic-function-generator`.

## Usage requirements

### Node.js

### SAM CLI

### AWS Credentials

### Deploy the dependency layer
The monitoring add-on has some dependencies on certain npm packages.
To avoid that each synthetic function package has to bring these dependencies, the use of a dependency layer proved to be the most efficient way.
The file `dependencyLayer.zip` represents a ready-to-deploy package to add the required dependencies as a Lambda Layer. Follow the instructions on `AWS Console -> Lambda -> Additional resources -> Layers` to deploy the package.
The dependency layer will be assigned a **Version ARN**, which is needed to generate functions using the CLI.
For more information about AWS Lambda Layers, see [here](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html).

### Create a AWS Lambda role for the synthetic functions
The synthetic Lambda functions need to be assigned a role ARN to allow the function to access other AWS services.
Either create a new role that defines what services the Lambda functions are allowed to access or use an existing role.
Make sure that AWS Lambda is listed in the **Trusted Entities** section so that it can be used by Lambda.
To generate synthetic functions using the CLI the **Role ARN** will be needed.

## Usage

The CLI offers descriptions and help texts for each command as well as an overall usage description:

```
CLI for generating AWS Lambda deployable synthetic function packages
This application can be used to generate a large amount of synthetic functions
that can be deployed to AWS Lambda for all kinds of measurements.
It also offers the capability to run invocations against those deployed functions.

Usage:
  synthetic-function-generator [command]

Available Commands:
  clean       Command to clean the build directory
  generate    Used to create serverless function packages
  help        Help about any command
  runload     Start load run for measurement data

Flags:
  -h, --help   help for synthetic-function-generator

Use "synthetic-function-generator [command] --help" for more information about a command.
```

### Command `generate`

```
This command generates AWS Lambda deployable serverless function artifacts.
The generated artifacts are compatible for use with the runload command.

Usage:
  synthetic-function-generator generate [flags]

Flags:
  -d, --dependency-layern-arn string   The ARN of the dependency layer, see README (required)
      --exclude string                 Path to file containing roll strings to be excluded from generation (use --save flag for an example)
  -f, --func-segments string           Path to function segments to be used for generation (required)
  -h, --help                           help for generate
  -l, --lambda-role-arn string         The ARN of the lambda role, see README (required)
  -m, --max-roll int                   Maximum number of rolled function segments (ignored if replayFile is provided) (default 3)
  -n, --num-funcs int                  Number of functions to generate (ignored if replayFile is provided) (default 1)
      --replay string                  Path to file containing roll strings to be regenerated (use --save flag for an example)
      --save                           Whether the generated function combinations should be saved
  -s, --sizes ints                     Specify function sizes to be generated, need to be supported by the platform! (default [128,416,704,992,1280,1568,1856,2144,2432,2720,3008])
```

### Command `runload`

```
This command drives load on the generated functions on lambda.
It writes the results on the local filesystem specified by the flag --result-dir for later evaluation.

Usage:
  synthetic-function-generator runload [flags]

Flags:
  -d, --duration int        Target duration in seconds (default 10)
  -f, --func-dir string     Directory containing the lambda functions (required)
  -h, --help                help for runload
  -p, --req-per-sec int     Target requests per second (default 50)
  -r, --result-dir string   Directory for the measurement results (default "./result-data")
  -w, --workers int         Number of parallel workers (default 5)
```

### Command `clean`

```
This command can be used to delete all generated functions

Usage:
  synthetic-function-generator clean [flags]

Flags:
  -h, --help   help for clean
```

## Implementing additional segments
