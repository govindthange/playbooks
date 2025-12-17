# How to optimize the long **To Be Read (TBR)** list of books?

Use the below prompt and follow the steps.

```javascript
{
    "instruction_profile": {
      "task": "Create a strictly structured incremental learning roadmap",
      "subject_input": "Variable: Subject Title",
      "resource_input": "Variable: Raw Command Output/List of resources",
      "output_format": "Markdown with explicit horizontal rules (---) between sections",
      "constraints": [
        "Reorder items based on educational logic (Foundation Concepts -> Application -> Mastery). If appropriate suggest appraoch like: History/Background -> Problem/Challenge -> Solution -> Pros/Cons of the Solution -> Implementation of the Solution.",
        "Section 1 and 3 MUST be a Markdown Table with exactly these columns: | Order | Book/Resource Title | Why this stage? | Key Takeaways |",
        "Suggest one or more books/resources to drop based on redundancy, outdated editions, or low pedagogical value.",
        "Provide a revised and optimized final list which covers 80 to 90% of the concept even after dropping the list.",
        "Perform a gap analysis explaining why the omitted content is non-critical.",
        "Use Markdown formatting (headings, bolding, lists) for readability.",        
        "Tone: Empathetic, insightful, and technical."
      ],
      "required_sections": [
        {
          "id": "Section 1",
          "header": "## Section 1: The Incremental Roadmap",
          "requirement": "Provide a table mapping the progression from 'Entry Point' to 'Mastery Phase'."
        },
        {
          "id": "Section 2",
          "header": "## Section 2: The 'Drop' List",
          "requirement": "Bullet points explaining specific resources to skip and technical reasons why."
        },
        {
          "id": "Section 3",
          "header": "## Section 3: The 80/20 Optimization (The Shortcut)",
          "requirement": "Select revised and most optimized set of books/resources that cover 80%-100% of knowledge. Define the percentage of proficiency reached and the logic of the shortcut."
        },
        {
          "id": "Section 4",
          "header": "## Section 4: The 'Knowledge Gap' Analysis",
          "requirement": "Identify what is lost from specific dropped titles and justify why those gaps are acceptable."
        }
      ]
    },
    "input_data": {
      "subject_title": "[INSERT SUBJECT HERE]",
      "raw_command_output": "[PASTE RAW FILE LIST OR RESOURCES HERE]"
    },
    "response_guidelines": {
      "formatting_rules": [
        "Use bolding for stage names (e.g., **The Foundation**).",
        "Tables must use standard Markdown syntax.",
        "Use horizontal rules to separate the four sections."
      ]
    }
  }

```

## Step 1. List all the books in your system folder. 

Run `find . -type f` in your target directory.

```
govind@thinkpad:~/books/e-books/python$ find . -type f -name "*.pdf" | sort
./1a_LearnPythonInOneDayAndLearnItWell_JamieChan_ed2_2017.pdf
./1b_PythonWorkbook_LearnPythonInOneDayAndLearnItWell_JamieChan_ed2_2019.pdf
./2_HeadFirstPython_PaulBarry_ed2_2017.pdf
./3_PythonCrashCourse_EricMatthes_ed2_2019.pdf
./4_AutomateTheBoringStuffWithPython_AlSweigart_ed2_2020.pdf
./5_FluentPython_LucianoRamalho_ed2_2022.pdf
./byte-of-python.pdf
./LearnMorePython3TheHardWay_Shaw_ZedA_2018.pdf
./LearnPython3TheHardWay_ZedAShaw_2017.pdf
./LearnPythonInOneDayAndLearnItWell_JamieChan_ed1_2014.pdf
./PythonCrashCourse_EricMatthes_ed1_2016.pdf

```

## Step 2. Update the subject in prompt.

Change `"subject_title"` to your new topic.

## Step 3. Update the command output in prompt.

Replace the text in `"raw_command_output"` with the output received in **Step #1**.

## Step 4. Submit the prompt.

After updating the values inside the `"input_data"` object submit the below JSON block to the AI.

