# jsPsych Self-Paced Reading Experiment

This repository contains two versions of a self-paced reading experiment implemented with jsPsych.

## Live Demo

Try the self-paced reading demo online: [SPR Demo](https://tamura-jspsych-demo.netlify.app/spr-demo.html)

## Repository Structure

- `full-experiment/`: Research data collection template with consent, attention checks, counterbalanced stimuli, Prolific URL parameter handling, and Firebase Realtime Database saving.
- `sample-experiment/`: Simplified educational demo of the core self-paced reading task. This version is intended for teaching, testing, and demonstration rather than production data collection.

## Features

### Core Self-Paced Reading Paradigm

- Moving window self-paced reading presentation.
- Reading time measurement for each word segment.
- Comprehension questions after each sentence.
- Practice trials with feedback before the main experiment.
- Monospace display for consistent text layout.

### Full Experiment Features

- Consent, warning statement, fullscreen mode, task instructions, attention checks, and debriefing.
- Latin square design with four counterbalanced lists: A, B, C, and D.
- Explicit `selected_list_id` storage for the selected counterbalanced list.
- Prolific identifiers read from URL parameters instead of participant text entry.
- Firebase Anonymous Authentication before the experiment starts.
- Firebase Realtime Database saving under an authenticated UID path.
- Checkpoint saves after practice, at the main-task midpoint, and immediately before final save.
- Completion code display only after final data saving succeeds.

### Sample Experiment Features

- Shorter demo focused on the core self-paced reading flow.
- Direct data display at the end of the experiment.
- Minimal setup for classroom or teaching use.

## Firebase Setup

The full experiment requires your own Firebase project before it can collect research data.

1. Create a Firebase project at [firebase.google.com](https://firebase.google.com/).
2. Enable Realtime Database.
3. Enable Authentication, then enable the Anonymous sign-in provider.
4. In `full-experiment/spr-full-demo.html`, replace the placeholder Firebase config with your own client-side Firebase web app config:

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
```

5. In Firebase Console, set the Realtime Database Security Rules using `firebase-database.rules.json`.
6. Replace `YOUR_COMPLETION_CODE` in `full-experiment/spr-full-demo.html` with the completion code for your Prolific study.
7. Before production deployment, set `TEST_MODE = false` in `full-experiment/spr-full-demo.html`.

Do not use `.write: true` for production data collection. The included rules file is intentionally simple: it denies database-wide reads, requires Firebase Authentication for writes, allows users to write only under their own UID, validates that the payload metadata matches the UID and local session ID in the path, restricts checkpoint names, and prevents overwriting existing checkpoint records.

The `final` record can be written again within the same Firebase UID and local session ID. This is intentional: if Firebase receives the final data but the browser misses the success response because of a temporary connection problem, the participant can use the retry button without being blocked by the rules. This browser-only template cannot prevent a participant from changing a payload before sending it; use a server-side API if strict tamper resistance is required.

Firebase Web API keys are expected to appear in browser-side Firebase config. They are not service account keys. Never commit service account JSON files, private keys, secret keys, or other server-side credentials to this repository.

## Firebase Data Paths

The final response is saved to:

```text
responses/{firebase_uid}/{local_session_id}/final
```

Checkpoints are saved to:

```text
responses/{firebase_uid}/{local_session_id}/checkpoints/{checkpoint_name}
```

Current checkpoint names:

- `practice_complete`
- `main_midpoint`
- `pre_final`

The Security Rules allow only these checkpoint names, and each checkpoint can be created only once.

Saved records include metadata and sanitized trial data. The metadata includes:

- `local_session_id`
- `prolific_pid`
- `study_id`
- `prolific_session_id`
- `firebase_uid`
- `selected_list_id`
- `saved_at`

The `saved_at` value uses `firebase.database.ServerValue.TIMESTAMP`, so the timestamp is assigned by Firebase Realtime Database rather than the participant's browser clock.

## App Check

Firebase App Check is optional for this template, but it is strongly recommended for production studies as an additional abuse-prevention layer. App Check can help reject traffic from unauthorized clients, but it does not eliminate all forms of abuse. This repository does not implement App Check by default because setup depends on your hosting environment and study deployment plan.

## Prolific Setup

Set the Prolific external study URL so that Prolific passes the participant, study, and session identifiers to the experiment:

```text
?PROLIFIC_PID={{%PROLIFIC_PID%}}&STUDY_ID={{%STUDY_ID%}}&SESSION_ID={{%SESSION_ID%}}
```

For example:

```text
https://your-study-host.example/full-experiment/spr-full-demo.html?PROLIFIC_PID={{%PROLIFIC_PID%}}&STUDY_ID={{%STUDY_ID%}}&SESSION_ID={{%SESSION_ID%}}
```

Use Prolific's participant access limit to control how many participants can enter the study at the same time.

By default, `TEST_MODE = true` in `full-experiment/spr-full-demo.html`. In this mode, if the experiment is opened locally or outside Prolific, missing URL parameters are stored as `"missing"` so the experiment can be tested without manual Prolific ID entry.

Before production deployment, change `TEST_MODE` to `false`. In production mode, if `PROLIFIC_PID`, `STUDY_ID`, or `SESSION_ID` is missing from the URL, the experiment will not start and will show an error message instead.

## Production Checklist

Before launching a real study, confirm that:

- `TEST_MODE = false` in `full-experiment/spr-full-demo.html`.
- The Firebase config has been replaced with your own project values.
- `YOUR_COMPLETION_CODE` has been replaced with the correct Prolific completion code.
- Realtime Database Rules from `firebase-database.rules.json` have been applied in Firebase Console.
- Firebase Anonymous Authentication is enabled.
- The Prolific external study URL includes `PROLIFIC_PID`, `STUDY_ID`, and `SESSION_ID`.
- Prolific participant access limits are set for the study.

## Data Handling

The data collected by the full experiment should be treated as pseudonymised data, not anonymous data. The research data does not contain participant names or contact details, but it does store Prolific participant ID, Prolific study ID, Prolific session ID, Firebase UID, local session ID, and selected list ID.

The local session ID is generated in the browser with `crypto.randomUUID()` when available, with a simple fallback for older browsers. This local session ID is included in the database path to reduce accidental overwrite risk.

## Data Output and Analysis

Each jsPsych trial creates a data object containing timing, stimulus, response, comprehension accuracy, and trial metadata. The full experiment filters `exp_item: true` records for checkpoint and final saving.

Raw JSON data usually needs transformation before statistical analysis. For an example of data transformation, see `sample-experiment/sample-data-transformation.html` and `sample-experiment/sample-data-output.txt`.

## Limitations

This public template improves the safety of the browser-only Firebase workflow, but it does not provide complete security for every study.

For large-scale data collection, strict rate limiting, payload validation, abuse prevention, or stronger server-side control, place a server-side API between the experiment and the database. Suitable options include Cloud Functions, Netlify Functions, Vercel Functions, or another backend service that validates and writes responses on behalf of the browser.

## Technical Requirements

- Modern browser with JavaScript enabled.
- Internet connection for loading jsPsych and Firebase libraries from CDNs.
- Firebase project with Realtime Database and Anonymous Authentication enabled for the full experiment.
- Hosting that can serve the HTML file to participants.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

## Credit and Acknowledgments

This project was built based on the tutorial "Week 4 practical | Online Experiments for Language Scientists" by Kenny Smith:
[https://kennysmithed.github.io/oels2020/oels_practical_wk4.html](https://kennysmithed.github.io/oels2020/oels_practical_wk4.html)

The original tutorial is licensed under a [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).

## Notes

This project was developed with assistance from AI tools including ChatGPT and Claude. While the experiment functions properly for its intended purpose, there may be opportunities for code improvements from a professional development perspective. Contributions, suggestions, and feedback from experienced developers are welcome.

## Citation and Reference

If you use this code in your research, please cite:

Tamura, Y. (2025). jsPsych Self-Paced Reading Experiment [Computer software].
https://github.com/tam07pb915/spr-jspsych-experiment

## Contact

For questions or correspondence, please contact:

Yu Tamura (Kansai University, Japan)  
Email: yutamura@kansai-u.ac.jp
