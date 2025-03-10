# jsPsych Self-Paced Reading Experiment

This repository contains two versions of a self-paced reading experiment implemented using jsPsych, a JavaScript library for creating behavioral experiments that run in a web browser.

## Live Demo

Try the self-paced reading demo online: [SPR Demo](https://tamura-jspsych-demo.netlify.app/spr-demo.html)

## Repository Structure

This repository is organized into two main components:

- `full-experiment/`: Complete experimental setup used for research
- `sample-experiment/`: Simplified demonstration of core self-paced reading functionality

## Features

### Core Self-Paced Reading Paradigm (Both Versions)

- **Moving Window Technique**: This implementation uses the moving window paradigm where text segments are revealed one at a time as participants press a key, while previous segments are masked
- **Reading Time Measurement**: Records the time taken to read each segment as the primary dependent variable
- **Comprehension Questions**: Follows each sentence to ensure participants are reading for understanding
- **Practice Trials**: Includes practice trials with feedback before the main experiment
- **Custom Styling**: Monospace font ensures consistent text display for accurate timing

### Full Experiment Additional Features

- **Participant Management**: Consent forms, demographics collection, and debriefing
- **Experimental Design**: Implements a Latin square design with counterbalanced stimuli
- **Data Storage**: Firebase integration for secure data collection (requires configuration)
- **Progress Tracking**: Includes progress bar and midpoint break
- **Attention Checks**: Implements mechanisms to identify careless responding
- **Fullscreen Mode**: For standardized presentation
- **Experimental Controls**: Randomization with constraints between trials

### Sample Experiment Features

- **Simplified Design**: Focuses only on core self-paced reading functionality
- **Direct Data Visualization**: Displays collected data at the end of the experiment
- **Educational Purpose**: Includes clear documentation within the code
- **Minimal Dependencies**: Uses only essential jsPsych plugins
- **Reduced Trial Count**: Shorter experiment for demonstration purposes

## Usage

### Sample Experiment

Perfect for:
- Learning how self-paced reading experiments work
- Testing basic functionality
- Teaching experimental methods
- Quick demonstrations

### Full Experiment

Designed for:
- Actual data collection for research
- Complete experimental workflow
- Integration with participant recruitment platforms
- Comprehensive data storage and management

### Firebase Integration

The full experiment version includes Firebase integration for data storage, but **you must configure it yourself**:

1. Create your own Firebase project at [firebase.google.com](https://firebase.google.com/)
2. Replace the Firebase configuration object in the HTML code with your own credentials:
   ```javascript
   const firebaseConfig = {
     apiKey: "YOUR_API_KEY",
     authDomain: "YOUR_AUTH_DOMAIN",
     databaseURL: "YOUR_DATABASE_URL",
     projectId: "YOUR_PROJECT_ID",
     storageBucket: "YOUR_STORAGE_BUCKET",
     messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
     appId: "YOUR_APP_ID"
   };
3. Set up appropriate security rules in your Firebase console

### Data Output and Analysis
The experiment produces JSON data in the following format:
- Each trial generates a data object with specific properties
- Reading time (rt) is recorded in milliseconds for each word segment
- Comprehension question accuracy is stored
- Trial metadata (item ID, condition, etc.) is included

Data transformation is necessary for analysis:
- The raw JSON output needs processing to extract reading times by condition
- You will need to aggregate data across participants and stimuli
- Analysis typically involves comparing reading times across conditions

Example data processing code will be provided in a separate file. Please see `/sample-experiment/sample-data-output.txt`

**Sample Analysis Showcase**: For a demonstration of how to transform and analyze the data from this experiment, see my [R Markdown document](https://tam07pb915.github.io/spr-jspsych-experiment/sample-experiment/sample-data-transformation.html).

### Technical Requirements

- Modern web browser with JavaScript enabled
- Internet connection for loading jsPsych libraries
- Firebase account (for full experiment only)

### License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
The MIT License is a permissive license that allows you to freely use, modify, distribute, and even sell this software, provided that you include the original copyright notice and the license text in any substantial portions of the software.

## Credit & Acknowledgments

This project was built based on the tutorial "Week 4 practical | Online Experiments for Language Scientists" by Kenny Smith:
[https://kennysmithed.github.io/oels2020/oels_practical_wk4.html](https://kennysmithed.github.io/oels2020/oels_practical_wk4.html)

The original tutorial is licensed under a [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/). This means you are free to:
- Share — copy and redistribute the material in any medium or format
- Adapt — remix, transform, and build upon the material for any purpose, even commercially

Under the following terms:
- Attribution — You must give appropriate credit, provide a link to the license, and indicate if changes were made.

My implementation builds upon the concepts and code presented in the tutorial while making substantial modifications and additions for specific experimental purposes.

## Notes

This project was developed with assistance from AI tools including ChatGPT and Claude. While the experiment functions properly for its intended purpose, there may be opportunities for code improvements from a professional development perspective. Contributions, suggestions, and feedback from experienced developers are welcome.

### Citation & Reference
If you use my code in your research, please cite:

Tamura, Y. (2025). jsPsych Self-Paced Reading Experiment \[Computer software\].
https://github.com/yourusername/jspsych-spr-experiment

### Contact
For questions or correspondence, please contact:

Yu Tamura (Kansai University, Japan)
Email: yutamura@kansai-u.ac.jp
