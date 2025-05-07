---
title: Compare the extensibility options for Azure Logic Apps (Standard)
description: Select the best extensibility option for your Azure Logic Apps (Standard) implementation.
contributor-type: community #Required. Don't change this value.
author: sebastianmeyer-1 #Required. GitHub alias for article author.
ms.author: mlcc-owners #Required. Don't change this value.
ms.reviewer: wsilveira, estfan #Required for Microsoft owners and reviewers. Don't change these values.
ms.service: azure #Required. Don't change this value.
ms.topic: how-to #Required. Don't change this value.
ms.date: 05/08/2025 #Required in mm/dd/yyyy format.
---

# Compare the extensibility options for Azure Logic Apps (Standard)

---

**Principal author**: [Sebastian Meyer](https://github.com/sebastianmeyer-1)

---

Azure Logic Apps (Standard) is an exceptionally powerful low-code/no-code solution for designing and automating business processes. It caters for developers of all levels of experience by offering flexibility in implementing workflows.

As business processes complexity grows, they can become harder to represent those solutions in a low-code format while providing the same level of operational efficiency and performance organization requires.

In these cases, Azure Logic Apps (Standard)empower you to blend low-code and pro-code solution together, reducing complexity of the workflow solution, while maintaining the same level of operational efficiency and performance desired.

Azure Logic Apps (Standard) provides several avenues to blend low-code with pro-code, depending on your solution needs. - whether you’re migrating legacy BizTalk solutions, managing simple code snippets, or integrating with serverless architectures:

-   **Execute Inline Code**
-   **Custom Code**
-   **Azure Functions**

Let’s dive into each option, including practical advice, comparison criteria, and additional operational considerations.

## Execute inline Code

Azure Logic Apps (Standard) provide actions that allow for inline code execution in JavaScript, PowerShell, or C\# Script. This approach is ideal when you need to quickly introduce logic without introducing external dependencies or additional deployment pipelines.

### Key Characteristics:

-   **Simplicity & Integration -** Inline code is embedded directly within your workflow, meaning you don’t have to manage separate deployments or services. This close coupling makes it perfect for lightweight tasks, such as data validation or small business rules.
-   **Provided in multiple language variants-**  This is the only pro-code solution provided by Azure Logic Apps (Standard) that is supported in multiple languages. The languages supported currently are:
    -   [C\#](/azure/logic-apps/add-run-csharp-scripts)
    -   [Javascript](/azure/logic-apps/add-run-javascript)
    -   [PowerShell](/azure/logic-apps/add-run-powershell-scripts)
-   **Available for cloud and local development -** Being fully embedded in the Azure Logic Apps (Standard) designer, Inline code actions are available for authoring in both the Azure portal and VS Code experiences.

NOTE: Javascript inline code action is also available in Azure Logic Apps (Consumption)

### Additional Considerations

-   **Dependencies support -** Inline code action do not support the injection of dependencies, like custom C\# assemblies. This might need to be taken in consideration if your code logic is dependent on components that are not readily available in the supported version of .NET.
-   **Debugging -** While inline code can simplify development by keeping everything in one place, debugging complex logic might be challenging. Azure Logic Apps (Standard) designer offers some rudimentary support for troubleshooting, providing access to the execution logs during test, so for complex logic, other options that allow for testing and debugging the logic in isolation are advised.
-   **Versioning & Deployment -**Since inline code is part of the workflow definition, you can’t control the code version independent of the workflow it is embedded on. That also means that if your code is required for multiple workflows in the same application, a copy of that code needs to be inserted on each workflow. For components that require independent versioning or use within multiple workflows, this option is not advised.

## Custom Code

Custom Code in Azure Logic Apps (Standard) offers a seamless way to incorporate more sophisticated programming logic, including legacy code written in .NET Framework, making it a good tool when modernizing legacy BizTalk solutions or extending workflows. This approach is particularly valuable if you need to implement complex logic, reuse business logic within multiple workflows or perform operations that exceed what inline code can comfortably handle.

### **Key Characteristics**

-   **Based on Azure Functions programming model** - A custom code action is based on the same programming model used by Azure Functions. This action is a new function, deployed in the same host as the Azure Logic Apps (Standard) application, which uses a new  trigger binding - the WorkflowActionTrigger. This allows workflows to invoke that action natively during its execution without needed to execute an http request.
-   **Integrate with existing code -** Incorporate custom reusable components, be it a legacy components written in .NET Framework or a a modern .NET component in your workflows, by refactoring them as custom code functions that directly integrate into your Logic Apps.
-   **Encapsulate complex processes -**Move complex logic from your workflow, simplifying your overall solution, and having better control of your complex components
-   **Simplify your deployments -** Custom code functions become part of your Logic Apps Workspace, making them available across multiple workflows in the target application. This tight coupling reinforces code reusability and central management, simplifying your deployments and source control.
-   **Support for Different .NET Versions -** You can [create custom code projects](/azure/logic-apps/create-run-custom-code-functions) using one of the following versiond of .NET:
    -   .NET Framework - reuse legacy components in application modernization scenarios, like BizTalk Migration,
    -   .NET 8 - extend your application without creating additional Azure resources, using modern .NET components.

### **Additional Considerations**

-   **Testing & Quality Assurance -** Since custom code approaches are more complex, ensure that these modules are thoroughly unit tested. Consider integrating automated testing pipelines using Azure DevOps or GitHub Actions.
-   **Maintainability -** With greater complexity comes the need for disciplined code reviews, robust logging, and clear documentation to manage ongoing updates and deployments.
-   **Performance & Scaling -** While custom code offers more control, closely monitor for performance bottlenecks. Integrate distributed tracing and Application Insights to track issues across the integrated Azure Logic Apps (Standard) ecosystem.

## Azure Functions

For organizations that are heavily invested in serverless computing, Azure Functions present a robust and scalable method to integrate existing business logic directly into your workflows.

### **Key Characteristics**

-   **Code Reusability & Scalability -** Azure Functions allow you to reuse business logic written in several supported languages, with the added benefit of independent scaling. If you already have robust business functions deployed, [calling them from Azure Logic Apps](/azure/logic-apps/call-azure-functions-from-workflows) avoids redundant development and code duplication.
-   **Operational Considerations -** This approach introduces external dependencies, meaning you must manage separate deployments, version controls, and potential integration complexities between Logic Apps and Azure Functions.
-   **Integration & Supported Languages –** Azure Functions [support multiple languages and frameworks](/azure/azure-functions/supported-languages?tabs=isolated-process%2Cv4&pivots=programming-language-csharp), which might be useful when your business logic requires a specific domain or dependency components.

### **Additional Considerations**

-   **Monitoring & Logging:** Utilize Application Insights and Azure Monitor to understand performance, track errors, and investigate exceptions across both Azure Functions and Logic Apps.
-   **Versioning and DevOps:** Maintain independent versioning of your code in Azure Functions while ensuring that Logic Apps workflows are synchronized with any changes disrupting the API contracts.
-   **Security:** Secure communication between Logic Apps and Azure Functions using managed identities or API keys.

## **Comparative Analysis**

A clear side-by-side evaluation can help determine which approach is best for your needs:

| **Approach**        | **Ideal Use Case**                                                 | **Pros**                                                                            | **Cons**                                                                         | **Deployment Considerations**                                                                           |
|---------------------|--------------------------------------------------------------------|-------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| **Inline Code**     | Simple data transformation, validations, light tasks               | - Minimal overhead <br> - Direct integration <br> - No external dependencies                  | - Limited debugging for complex logic <br> - Versioning tied to workflow              | - Deployed within a Logic App workflow, version is always attached to workflow.                         |
| **Custom Code**     | Migrating legacy systems, advanced processing                      | - Reuse of existing code <br> - Centralized functions within the same Logic Apps project | - Increased solution complexity <br> - Independent functions require its own testing                                | - Deployed as part of the Azure Logic App (Standard) application, minimizing surface area of deployment |
| **Azure Functions** | Existing serverless functions, independent scalable business logic | - Independent scaling <br> - Reuse of serverless patterns - Language versatility         | - Introduces external dependencies <br> - Requires separate deployment and monitoring | - Independent deployment allowing for independent scale. <br> - Versioning must be carefully managed.        |

## **Conclusion**

Logic Apps Standard is a versatile platform that bridges the gap between low-code simplicity and the power of pro-code development. By choosing the right approach—whether inline code for simplicity, custom code for migration and reusability, or Azure Functions for scalability—developers can craft solutions that are robust, maintainable, and future-proof.

The key is understanding your requirements: balance development speed with long-term maintainability, consider performance implications, and always design with security and testing in mind. As you expand the capabilities of your Logic Apps, remember that the best solutions come from a careful evaluation of both the technical and operational landscapes.
