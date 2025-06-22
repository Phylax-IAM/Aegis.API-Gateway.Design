# Aegis.API-Gateway.Design Repository

<p align="center"> 
    <img src="./diagrams/logo/Aegis API Gateway Logo.png"/>
</p>

---

📘 **Central source for architecture and API design docs for Aegis API Gateway**

---


## 🧩 What Is This Repository?

This repository holds all **Design Documents** related to the **Aegis API Gateway** suite, specifically focused on the **API Design and Specifications**. It includes:

- High-level architecture, sequence and component diagrams  
- Configurations and features specifications  
- Data models, sequence diagrams, and security flows
- Start looking here: [System Design Document](./system-design.md)  

---

## 📂 Folder Structure

```text
/
├── architecture/                                   
│   ├── component-diagram.md                               
│   ├── high-level-diagram.md                                     
|   └── sequence-diagram.md          
├── decisions/                                            
|   └── ADR-001-routing.md
├── diagrams/                                   
│   ├── draw.io/                               
│   |   ├── component-diagram.dio               
│   |   ├── high-level.dio              
│   |   ├── sequence-diagram.dio                 
|   ├── img/                               
│   |   ├── component-diagram.png               
│   |   ├── high-level.png              
│   |   ├── sequence-diagram.png                 
|   └── logo/                                    
│       └── system-context.design.png
├── references/                                   
|   └── research.md
├── specs/                                   
│   ├── config-spec.yaml                                 
|   └── feature-spec.yaml
├── CHANGELOG.md                                  
├── functional-requirements.md                     
├── LICENSE                        
├── README.md                               
└── system-design.md                                 
```

---

## ✅ Getting Started

1. **Clone the Repo**

   ```bash
   git clone https://github.com/Phylax-IAM/Aegis.API-Gateway.Design.git
   ```

2. **Explore the Design Docs**

   * Diagrams in `/diagrams/img/`
   * Logos in `/diagrams/logo/`

3. **Your Feedback & Contributions**

   * Hit the Issues tab or open a PR

---

## 📋 Key Documents Explained

* **`system-design.md`** – Serves as the primary reference for all high-level architectural decisions, system context, and component interactions within the Phylax IAM platform.
  
* **`functional-requirements.md`** – Lists and explains the core functional requirements of the API Gateway MVP, including routing, rate limiting, caching, and more.

---

## 🛠 How to Use These Docs

* As a **reference for frontend/backend implementation**
* For **auto-generating mock servers or tests** from OpenAPI spec
* To maintain **naming/response consistency across phases**
* As source-of-truth during architecture reviews or onboarding

---

## 🤝 Contributing

Please follow these conventions:

* Write clear and consistent API/field names
* Add diagrams or descriptions when changing or introducing new endpoints
* Use [Conventional Commit Messages](https://www.conventionalcommits.org/en/v1.0.0/)

---

## 📄 License

[MIT License](./LICENSE) – See LICENSE file for details.

---

## 🔗 Contacts

For questions or feedback, reach out to:

* **Authored by**: *Pragyanshu Rai* (`<pragyanshu.rai.phylax.iam@gmail.com>`)
* **Project Lead**: **Pragyanshu Rai**