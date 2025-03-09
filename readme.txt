# Canvas LMS API Setup for MiraCosta Students

This guide explains how to obtain your **Canvas API Key**, set up a **Custom GPT** with the provided **OpenAPI schema**, and use it to retrieve **courses and assignments**.

---

## **Step 1: Get Your Canvas API Key**

To connect your **Custom GPT** to MiraCosta’s Canvas LMS, you need to generate an API key.

1. **Log into Canvas**\
   Go to **[MiraCosta Canvas](https://miracosta.instructure.com/)** and log in using your student credentials.

2. **Navigate to API Key Settings**

   - Click on your **Profile Picture** (top-left or top-right).
   - Select **Settings** from the menu.
   - Scroll down to the section titled **Approved Integrations**.
   - Click **+ New Access Token**.

3. **Generate an API Key**

   - In the pop-up, enter a purpose (e.g., "Custom GPT Canvas Access").
   - Click **Generate Token**.
   - Copy the **Access Token** (this is your API key).
   - Store it in a secure place (you won't be able to see it again once you leave the page).

---

## **Step 2: Add the API Key to Your Custom GPT**

To make authenticated requests, you'll need to input your API key into your **Custom GPT’s Actions settings**.

1. **Open ChatGPT & Create a Custom GPT**

   - Go to **[ChatGPT](https://chat.openai.com/gpts)**.
   - Click **Explore GPTs** → **Create a GPT**.

2. **Go to "Actions" and Enable API Calls**

   - Under the **Configure** tab, scroll to **Actions**.
   - Click **Enable** API calls and paste the **OpenAPI schema** (provided separately).

3. **Add Your API Key in Authentication**

   - In the Actions section, scroll to **Authentication**.
   - Choose **API Key** as the authentication method.
   - Paste your **Canvas API Key**.

---

## **Step 3: Paste in the OpenAPI Schema**

Copy and paste the **OpenAPI schema** into the "Actions" section of your **Custom GPT**. The schema allows your GPT to fetch:

- **List of your courses**
- **List of assignments per course**
- **A single assignment's details**
- **Filtered assignments (e.g., upcoming, ungraded, or within a specific date range)**


-------schema---------


openapi: 3.1.0
info:
  title: Canvas LMS Course and Assignment API
  description: Retrieve course and assignment details from Canvas LMS.
  version: 1.0.0

servers:
  - url: https://miracosta.instructure.com/api/v1

paths:
  /courses:
    get:
      operationId: listCourses
      summary: List all courses for the current user
      description: Retrieves a list of active courses the user is enrolled in.
      responses:
        "200":
          description: A list of courses
          content:
            application/json:
              schema:
                type: array
                items:
                  type: object
                  properties:
                    id:
                      type: integer
                      description: The unique ID of the course
                    name:
                      type: string
                      description: The name of the course
                    course_code:
                      type: string
                      description: The short identifier of the course

  /courses/{course_id}/assignments:
    get:
      operationId: listAssignments
      summary: List assignments for a course
      description: Retrieves assignments with optional filtering for date ranges, upcoming, or ungraded assignments.
      parameters:
        - name: course_id
          in: path
          required: true
          schema:
            type: integer
          description: The ID of the course
        - name: bucket
          in: query
          required: false
          schema:
            type: string
            enum: [past, overdue, undated, ungraded, unsubmitted, upcoming, future]
          description: Filter assignments by status
        - name: needs_grading_count_by_section
          in: query
          required: false
          schema:
            type: boolean
          description: If true, only return assignments that need grading
        - name: start_date
          in: query
          required: false
          schema:
            type: string
            format: date-time
          description: Start date filter for assignments
        - name: end_date
          in: query
          required: false
          schema:
            type: string
            format: date-time
          description: End date filter for assignments
      responses:
        "200":
          description: A list of assignments
          content:
            application/json:
              schema:
                type: array
                items:
                  type: object
                  properties:
                    id:
                      type: integer
                      description: The unique ID of the assignment
                    name:
                      type: string
                      description: The name of the assignment
                    due_at:
                      type: string
                      format: date-time
                      description: The due date of the assignment
                    needs_grading_count:
                      type: integer
                      description: Number of submissions that need grading

  /courses/{course_id}/assignments/{assignment_id}:
    get:
      operationId: getAssignment
      summary: Get details for a specific assignment
      description: Retrieve information about a specific assignment by its ID.
      parameters:
        - name: course_id
          in: path
          required: true
          schema:
            type: integer
          description: The ID of the course
        - name: assignment_id
          in: path
          required: true
          schema:
            type: integer
          description: The ID of the assignment
      responses:
        "200":
          description: Details of the assignment
          content:
            application/json:
              schema:
                type: object
                properties:
                  id:
                    type: integer
                    description: The unique ID of the assignment
                  name:
                    type: string
                    description: The name of the assignment
                  due_at:
                    type: string
                    format: date-time
                    description: The due date of the assignment
                  needs_grading_count:
                    type: integer
                    description: Number of submissions that need grading

---

## **Step 4: Add a Basic Prompt and Knowledge Base**

In the **Instructions** tab of your Custom GPT, include a prompt like:

> “This GPT utilizes the Canvas API to access and manage a student's assignments and courses. It can retrieve a list of active courses, view upcoming and past assignments, and check grading statuses. The GPT helps students stay organized by providing due dates, tracking ungraded assignments, and offering insights into coursework progress. If needed, it can filter assignments based on submission status, deadlines, or grading requirements. The GPT also can help give students a starting document for each assignment.”

Optionally, you can add a **knowledge base** (under the "Knowledge" tab) by uploading relevant Canvas documentation or course details.

---

## **Step 5: Test Your Custom GPT**

Once everything is set up:

- Click **Test Action** in the Actions tab to ensure your API key is working.
- Try asking:
  - “List my courses.”
  - “Show my upcoming assignments.”
  - “Get details for Assignment.”
  - "Create a completed document for each assignment in a canvas."

If you encounter issues, double-check your **API key** and ensure it’s still active.

---

## **Security Reminder**

🚨 **Never share your API key** with anyone. If you suspect unauthorized access, you can **revoke** the key from the **Canvas API settings** and generate a new one.

---

## **You’re All Set! 🚀**

Now your **Custom GPT** is fully integrated with **MiraCosta Canvas LMS**! 🎓🎉

