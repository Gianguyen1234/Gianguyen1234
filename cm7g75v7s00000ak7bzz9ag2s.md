---
title: "MVVM with Jetpack Compose:  CRUD App"
datePublished: Sat Feb 22 2025 12:49:36 GMT+0000 (Coordinated Universal Time)
cuid: cm7g75v7s00000ak7bzz9ag2s
slug: mvvm-with-jetpack-compose-crud-app
tags: jetpack-compose

---

Link Chat Gpt: [https://chatgpt.com/share/67b9e986-a894-800c-99c6-f7a43141732e](https://chatgpt.com/share/67b9e986-a894-800c-99c6-f7a43141732e)

Link Youtube:

For a CRUD (Create, Read, Update, Delete) app using **MVVM with Jetpack Compose**, you would typically structure your app like this:

### **Architecture Breakdown**

1. **View (Jetpack Compose UI)**
    
    * Displays data to the user.
        
    * Collects user inputs and forwards them to the `ViewModel`.
        
2. **ViewModel**
    
    * Holds UI state and exposes it via `StateFlow` or `LiveData`.
        
    * Calls repository methods to perform CRUD operations.
        
    * Uses `MutableState` in Compose for UI state management.
        
3. **Repository**
    
    * Acts as a single source of truth for data.
        
    * Fetches data from local database or remote API.
        
4. **Model (Data Layer)**
    
    * Represents the data structure.
        
    * Uses Room database for local persistence.
        
5. **Database (Room)**
    
    * Stores data locally.
        
    * Provides DAO (Data Access Object) for querying.
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740228516481/6a9cb6eb-8fd4-461a-8da8-660d53fe6ee7.png align="center")