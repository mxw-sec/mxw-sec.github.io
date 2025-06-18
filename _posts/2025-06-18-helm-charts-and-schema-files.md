# 2025-06-18 Helm Charts and Schema files

Hey Guys,

Long time since I wrote any form of blog post, so I figured I would just jump directly into the Chaos of Helm Charts and Schema Files.

<p class="callout info">**SCHEMA FILES ARE NOT REQUIRED BUT RECOMMENDED**</p>

### **HELM CHARTS AND SCHEMA**

If you are reading this post, then I am going to make the assumption that you are aware of what a helm chart is and how it works, but in the rare case that you aren't, here is the TLDR:  
Helm charts are collections of templates or Kubernetes manifests that are packaged together for the purpose of creating a repeatable deployment experience, as well as bundling all of the required items together.

### Helm Charts:

I am guessing that you have seen the many technical blogs that have something like

{% highlight bash %}
kubectl apply -f https://{somerandompath}/file.yaml
{% endhighlight %}

While that is great for a single resource, that happens when you have 4-5 or more files that all need to be deployed?  
What happens when you need to edit one of the values in the file for your environment?

Well this is where helm charts can really help, by using the templating language of GO, helm made it possible to store those manifests files and a "values" file together in a single bundle, more importantly, you can create your own values file and override the default definitions of the bundled file, allowing really quick updates and deployments.

But with that simplicity can come chaos, who is validating what your put in your file is actually a valid option, or what if you have a typo?

This is where the values.schema.json files can really help.

### Schema files:

Now the Schema file really is just a constraint file, it uses simple regex or statements to define what type of values can and can't be entered and performs any sanity check you want.

Here is a sample Values.Schema.Json file I am currently using:

{% highlight json %}
{
  "$schema": "https://json-schema.org/draft/2019-09/schema",
  "title": "Simplified .NET Chart Values",
  "description": "JSON Schema for Pacvue simplified .NET Helm chart values.yaml",
  "type": "object",
  "properties": {
    "imageUrl": {
      "type": "string",
      "description": "Base container registry URL"
    },
    "services": {
      "type": "object",
      "description": "Service definitions",
      "patternProperties": {
        "^[a-zA-Z0-9][a-zA-Z0-9-]*[a-zA-Z0-9]$": {
          "type": "object",
          "properties": {
            "appName": {
              "type": "string",
              "description": "Application name"
            },
            "revisionHistoryLimit": {
              "type": "integer",
              "minimum": 0,
              "maximum": 10,
              "default": 3
            },
            "nameSpace": {
              "type": "string",
              "default": "default"
            },
            "appEnv": {
              "type": "string",
              "description": "Application environment",
              "default": "us-dev",
              "enum": [
                "values"
              ]
            },
            "appProfile": {
              "type": "string",
              "default": "dev",
              "enum": [
                "values"
              ],
              "description": "Application profile"
            },
            "replicas": {
              "type": "integer",
              "minimum": 0,
              "maximum": 20,
              "default": 1
            },
            "memoryRequest": {
              "type": "string",
              "pattern": "^[0-9]+[GMK]i?$",
              "default": "1Gi"
            },
            "memoryLimit": {
              "type": "string",
              "pattern": "^[0-9]+[GMK]i?$",
              "default": "4Gi"
            },
            "imageName": {
              "type": "string"
            },
            "imageTag": {
              "type": "string",
              "default": "latest"
            },
            "healthPath": {
              "type": "string",
              "default": "/health"
            },
            "languageVersion": {
              "type": "string",
              "enum": ["6.0", "7.0", "8.0"],
              "default": "8.0"
            },
            "appStartCommand": {
              "type": "string",
              "default": "dotnet $(EXE_FILE)"
            },
            "exeFile": {
              "type": "string",
              "pattern": ".*\\.dll$"
            },
            "developer": {
              "type": "string"
            },
            "leader": {
              "type": "string"
            }
          },
          "required": ["appName", "revisionHistoryLimit"]
        }
      }
    }
  },
  "required": ["services", "developer", "leader", "appEnv", "imageUrl"]
}

{% endhighlight %}
