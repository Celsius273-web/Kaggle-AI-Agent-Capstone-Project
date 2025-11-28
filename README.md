# Kaggle-AI-Agent-Capstone-Project
I built an AI teaching assistant using Google's ADK (Agent Development Kit) that adapts to individual learners.


Project Overview

I designed an AI teaching assistant using the Google ADK  that provides personalized instruction to individual learners. This system was designed with two cooperating agents to teach content and evaluate and verify understanding while tracking student progress through one or several sessions. *Note: for the project I focused on creating the agents and not deploying them.

How It Works

TeacherBot takes the instruction side. It explains concepts from simple to complex, depending on what the student has learned so far. It can search Google for current information about topics and will also store what the student has learned.

GraderBot is called to mark student responses. Whenever the teacher wants to verify whether or not a student actually understood something, GraderBot is called to mark the answer and update the student's proficiency scores.

Both agents have access to a persistent storage that keeps track of the student's name, the topics they have learned, their proficiency levels on each topic, and any comments about their progress.

Key Concepts Demonstrated

1. Multi-Agent System

I set up the agent hierarchy where TeacherBot acts as the primary agent and can assign grading tasks to GraderBot. This is seen in the instructions to TeacherBot: "Use the `GraderBot` sub-agent to assess student learning."

The agents switch in turn—first, TeacherBot teaches, then GraderBot evaluates, and then back to TeacherBot to continue teaching considering the evaluation results.

2. Tools (Multiple Kinds)

I developed three custom-made tools that are used by the agents to keep track of the student data:

- save_userinfo() - Saves the name of the student, topics learned, progress percentage, and progress notes.
- retrieve_userinfo() - Retrieves previously saved student information.
- update_topic() - Updates proficiency scores for a particular topic and the timestamp for when it was last practiced. 

Google Search has also been integrated as a built-in tool for TeacherBot. This allows the agent to have access to current information on the topics it is teaching, especially on new information as well as get reliable sources of information.

3. Sessions & State Management

The system maintains state on two levels:

- Runtime state is managed using the built-in ADK state management. User-scoped keys like user:name and user:learned_topics are being used to keep track of information during active sessions.

- Persistent storage is written on a JSON file indexed by user ID. Thus, a student's progress survives even when the program is restarted. Once a student comes back after a while, their old progress would be loaded, and the system resumes from there.

Creation and retrieval of sessions are managed by the run_session() helper function to ensure that each student has an isolated conversation history.

4. Observability (Bonus)

I added the LoggingPlugin() to track what the agents are doing. It helps debug issues and understand how the agents are making decisions.

Technical Implementation Details

State Persistence: The _read_notes() and _write_notes() functions handle file I/O with proper error handling. Student data is stored as JSON along with the timestamp to keep track of when was the information last updated.

Data Structure: Each student's record includes:
- User name
- List of learned topics (each with topic_id, topic_name, proficiency score, and last_practiced timestamp)
- Overall progress percentage
- Progress notes (capped at 300 characters)
- Last updated timestamp

Error Handling: Try-except blocks are used around the code for all file operation and API calls. Besides that, a retry for the Gemini API with exponential backoff is provided to handle rate limits and temporary failures.

Model Configuration: Both agents use gemini-2.5-flash-lite for speed and cost-efficient. In a production teaching system, you want quick responses so that students are not waiting for a long time.

Significance

This is not just a chatbot answering questions. The multi-agent design separates the learning from the assessing to bring an honest and independent perspective to instruction and evaluation. Because of a persistent state, it is able to track learning across time, not just within a single conversation. The combination between custom tools (for state management) and built-in tools (for information retrieval) demonstrates how one can develop more specialized functionality while remaining connected to capabilities already offered.

The Code

The code needs a .env file with a GEMINI_API_KEY set as well as a JSON file to store the student progress. With this setup, you can create sessions and run queries using the run_session() helper function. From that point onward, the agents automatically take over, determining when to save progress, when to search for information, and when to assign work to the grader. NOTE: I have not created a way to deploy the program. For the sake of learning about AI agents in this capstone project I didn't make it deployable. 
