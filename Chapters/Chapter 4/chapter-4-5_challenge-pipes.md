# Chapter 4.5 - Challenge: Create Pipes to Improve Budget and Duration Formatting
Associated files are located in the `files/Chapter-4-5` folder
- Initial files
- Solution by Harry
- Solution provided

## In this challenge, we want to format two sets of data using two separate pipes
- Create a custom pipe that formats the movie budget as follows:
  - If the budget is "175", render it as "$175 million"
  - If the budget is a range such as "175-200", render it as "$175 to $200 million"
- Then create another custom pipe to format the movie duration
  - If the duration is "92", it is displayed as "1h 32min"

### Requirements
- Edit the provided `src/pipes/million-dollar.pipe.ts`
- Implement the transform method to format input values as defined in the challenge description:
  - If the budget is "175", render it as "$175 million"
  - If the budget is a range such as "175-200", render it as "$175 to $200 million"
- Add your pipe to the template of `movie-item.component.ts` and ensure the movie budgets are displayed as required
- Edit the provided `src/pipes/min-to-duration.pipe.ts`
- Implement the transform method to format input values as defined in the challenge description:
  - "92" must be displayed as "1h 32min"
- Add your pipe to the template of `movie-item.component.ts` and ensure the movie durations are displayed as required

## Sample Output
<img width="480" height="118" alt="image" src="https://github.com/user-attachments/assets/2c9bb6d6-196d-4cbe-8bed-92717c723142" />
