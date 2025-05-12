---
title: Compare the Extensibility Options for Azure Logic Apps (Standard)
description: Select the best extensibility option for your Azure Logic Apps (Standard) implementation.
contributor-type: community
author: sebastianmeyer-1
ms.author: mlcc-owners
ms.reviewer: wsilveira, estfan
ms.service: azure
ms.topic: how-to
ms.date: 05/08/2025
---

# Compare the extensibility options for Azure Logic Apps (Standard)

---

**Principal author**: [Sebastian Meyer](https://github.com/sebastianmeyer-1)

---

Azure Logic Apps (Standard) is a powerful low-code/no-code solution for designing and automating business processes. It caters to developers of all experience levels by offering flexibility in implementing workflows.

As the complexity of business processes grows, it can become harder to represent solutions in a low-code format while maintaining the same level of operational efficiency and performance that an organization requires. In these cases, Azure Logic Apps (Standard) empowers you to blend low-code and pro-code solutions together. It can help you reduce the complexity of a workflow solution.

Azure Logic Apps (Standard) provides options to blend low-code and pro-code solutions: inline code, custom code, and Azure Functions. Let's dive into each option, including practical advice, comparison criteria, and additional operational considerations. You can then choose an option based on your needs, whether you're migrating legacy BizTalk solutions, managing simple code snippets, or integrating with serverless architectures.

## Inline code

Azure Logic Apps (Standard) provides actions that allow for inline code execution in multiple languages. This approach is ideal when you need to quickly introduce logic without introducing external dependencies or additional deployment pipelines.

### Key characteristics

- **Simplicity and integration**: Inline code is embedded directly within your workflow, so you don't have to manage separate deployments or services. This close coupling makes it useful for lightweight tasks, such as data validation or small business rules.
- **Multiple language variants**: This is the only pro-code solution provided by Azure Logic Apps (Standard) that's supported in multiple languages. The currently supported languages are:
  - [C\#](/azure/logic-apps/add-run-csharp-scripts)
  - [JavaScript](/azure/logic-apps/add-run-javascript)
  - [PowerShell](/azure/logic-apps/add-run-powershell-scripts)
- **Availability for cloud and local development**: Because inline code actions are fully embedded in the Azure Logic Apps (Standard) designer, they're available for authoring in both the Azure portal and Visual Studio Code.

> [!NOTE]
> JavaScript inline code action is also available in Azure Logic Apps (Consumption).

### Considerations

- **Dependency support**: Inline code action doesn't support the injection of dependencies, like custom C\# assemblies. Consider this information if your code logic is dependent on components that aren't readily available in the supported version of .NET.
- **Debugging**: Although inline code can simplify development by keeping everything in one place, debugging complex logic might be challenging. The Azure Logic Apps (Standard) designer offers some rudimentary support for troubleshooting by providing access to the execution logs during tests. For complex logic, we recommend other options that allow for testing and debugging the logic in isolation.
- **Versioning and deployment**: Because inline code is part of the workflow definition, you can't control the code version independently from the workflow where it's embedded. If your code is required for multiple workflows in the same application, a copy of that code needs to be inserted in each workflow. For components that require independent versioning or use within multiple workflows, we don't recommend this option.

## Custom code

Custom code in Azure Logic Apps (Standard) offers a seamless way to incorporate more sophisticated programming logic, including legacy code written in the .NET Framework. This ability makes custom code suitable for modernizing legacy BizTalk solutions or extending workflows. This approach is particularly valuable if you need to implement complex logic, reuse business logic within multiple workflows, or perform operations that exceed what inline code can comfortably handle.

### Key characteristics

- **Based on Azure Functions programming model**: A custom code action is based on the same programming model that Azure Functions uses. This action is a new function that's deployed in the same host as the Azure Logic Apps (Standard) application. It uses a new trigger binding: `WorkflowActionTrigger`. Workflows can invoke that action natively during execution without the need to execute an HTTP request.
- **Integration with existing code**: You can incorporate custom reusable components by refactoring them as custom code functions that directly integrate into your logic apps. These reusable components could be legacy components written in the .NET Framework or modern .NET components in your workflows.
- **Encapsulation of complex processes**: The ability to move complex logic from your workflow simplifies your overall solution and gives you better control of your complex components.
- **Simplified deployments**: Custom code functions become part of your Logic Apps workspace, so they're available across multiple workflows in the target application. This tight coupling reinforces code reusability and central management, to help simplify your deployments and source control.
- **Support for multiple .NET versions**: You can [create custom code projects](/azure/logic-apps/create-run-custom-code-functions) by using one of the following versions of .NET:
  - **.NET Framework**: Reuse legacy components in application modernization scenarios, like BizTalk migration.
  - **.NET 8**: Extend your application without creating additional Azure resources, by using modern .NET components.

### Considerations

- **Testing and quality assurance**: Because custom code approaches are more complex, ensure that these modules are thoroughly unit tested. Consider integrating automated testing pipelines by using Azure DevOps or GitHub Actions.
- **Maintainability**: With greater complexity comes the need for disciplined code reviews, robust logging, and clear documentation to manage ongoing updates and deployments.
- **Performance and scaling**: Although custom code offers more control, closely monitor for performance bottlenecks. Integrate distributed tracing and Application Insights to track issues across the integrated Azure Logic Apps (Standard) ecosystem.

## Azure Functions

For organizations that are heavily invested in serverless computing, Azure Functions presents a robust and scalable method to integrate existing business logic directly into your workflows.

### Key characteristics

- **Code reusability and scalability**: Azure Functions allows you to reuse business logic written in several supported languages, with the added benefit of independent scaling. If you already deployed robust business functions, [calling them from Azure Logic Apps](/azure/logic-apps/call-azure-functions-from-workflows) avoids redundant development and code duplication.
- **Operational considerations**: This approach introduces external dependencies. You must manage separate deployments, version controls, and potential integration complexities between Logic Apps and Azure Functions.
- **Integration and supported languages**: Azure Functions [supports multiple languages and frameworks](/azure/azure-functions/supported-languages?tabs=isolated-process%2Cv4&pivots=programming-language-csharp). This support might be useful when your business logic requires a specific domain or dependency components.

### Considerations

- **Monitoring and logging**: Use Application Insights and Azure Monitor to understand performance, track errors, and investigate exceptions across both Azure Functions and Logic Apps.
- **Versioning and DevOps**: Maintain independent versioning of your code in Azure Functions while ensuring that Logic Apps workflows are synchronized with any changes that disrupt the API contracts.
- **Security**: Help secure communication between Logic Apps and Azure Functions by using managed identities or API keys.

## Comparative analysis

A clear side-by-side evaluation can help you determine which approach is best for your needs:

| Approach        | Ideal use case                                                 | Pros                                                                            | Cons                                                                         | Deployment considerations                                                                           |
|---------------------|--------------------------------------------------------------------|-------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| **Inline code**     | Simple data transformation, validations, light tasks               | - Minimal overhead <br> - Direct integration <br> - No external dependencies <br> - Cloud and local authoring tools                 | - Limited debugging for complex logic <br> - Versioning tied to workflow              | - Deployed within a Logic Apps workflow, so the version is always attached to the workflow                         |
| **Custom code**     | Migrating legacy systems, advanced processing                      | - Reuse of existing code <br> - Centralized functions within the same Logic Apps project | - Increased solution complexity <br> - Testing required for independent functions                                | - Deployed as part of the Logic Apps (Standard) application, minimizing the surface area of deployment |
| **Azure Functions** | Existing serverless functions, independent scalable business logic | - Independent scaling <br> - Reuse of serverless patterns <br> - Language versatility         | - Introduces external dependencies <br> - Requires separate deployment and monitoring | - Independent deployment, allowing for independent scale <br> - Requires careful management of versioning        |

## Conclusion

Logic Apps (Standard) is a versatile platform that bridges the gap between low-code simplicity and the power of pro-code development. You can choose among three approaches: inline code for simplicity, custom code for migration and reusability, or Azure Functions for scalability. By choosing the right approach, you can craft solutions that are robust, maintainable, and future-proof.

The key is understanding your requirements: balance development speed with long-term maintainability, consider performance implications, and always design with security and testing in mind. As you expand the capabilities of your logic apps, remember that the best solutions come from a careful evaluation of both the technical and operational landscapes.
