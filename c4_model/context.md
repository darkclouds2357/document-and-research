For each context in the C4 model, the type of content (text or image) you should create depends on the level and the target audience. Here's a breakdown of the content type suitable for each level:

## 1. Context Level
Content Type: Image and Text  
Image: A high-level diagram (e.g., a simple box diagram) that shows the system and its interactions with external actors (users, systems, and external services). This diagram should be easy to understand by non-technical stakeholders.  
Text: A brief description explaining the overall system, its purpose, and how it interacts with external entities. This might include details about users, other systems, and the environment in which the system operates.  
## 2. Container Level
Content Type: Image and Text  
Image: A container diagram that shows the major components (containers) within the system, including web applications, databases, microservices, and how they communicate with each other. This diagram should be detailed enough to give a clear understanding of the system's architecture.  
Text: Descriptions of each container, outlining their responsibilities, how they interact, and the technologies used. This can include API endpoints, communication protocols, and any other relevant technical details.  
## 3. Component Level
Content Type: Image and Text  
Image: Component diagrams for each container, detailing the internal structure and how different components interact within that container. These diagrams can include classes, modules, services, and other smaller building blocks.  
Text: Detailed descriptions of each component, explaining their role, responsibilities, and interactions. This might also include information about patterns used (e.g., CQRS, event sourcing), dependencies, and any specific implementation details.  
## 4. Code Level
Content Type: Text (with code snippets) and optionally Image  
Text: Code-level documentation, including class definitions, function signatures, and comments explaining the logic. This can also include UML class diagrams if needed to visualize relationships between classes or modules.  
Image (optional): Detailed UML diagrams or flowcharts that represent the code structure, such as class diagrams, sequence diagrams, or state diagrams. These help visualize how the code is structured and behaves.  
## Summary:
Context Level: Image (high-level overview) + Text (brief descriptions)  
Container Level: Image (container diagram) + Text (container descriptions)  
Component Level: Image (component diagrams) + Text (component descriptions)  
Code Level: Text (code snippets and explanations) + Optional Image (UML diagrams)  
By using a combination of images and text at each level, you can effectively communicate both the high-level architecture and the low-level implementation details to different stakeholders.  