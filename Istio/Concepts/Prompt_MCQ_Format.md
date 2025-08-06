# MCQ Generation Prompt

When you see this file, generate MCQs in the following format for the given topic. Each question should have:

- A clear question statement.
- Four or more choices, each on its own line, prefixed with **Choice:**
- The correct answer, prefixed with **Answer:**
- Each question block separated by a line of three dashes (---).

## Example Format

---

**Question 1:** What is the purpose of traffic management in a service mesh?  
**Choice:** To control how services communicate with each other  
**Choice:** To manage external traffic entering the application  
**Choice:** To store data in the mesh  
**Choice:** To ensure services are deployed correctly  
**Answer:** To control how services communicate with each other  

---

**Question 2:** Which component is responsible for managing external traffic entering the mesh?  
**Choice:** Virtual Service  
**Choice:** Gateway  
**Choice:** Destination Rule  
**Choice:** Pod Autoscaler  
**Answer:** Gateway  

---

**Instructions for MCQ Generation:**
- Cover the full scope of the topic (e.g., all subtopics of Traffic Management).
- Use the exact format above for every question.
- Do not include explanations, only the question, choices, and answer.
- Number questions sequentially.
- Each question must have only one correct answer.

---
