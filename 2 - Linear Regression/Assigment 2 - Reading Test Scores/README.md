READING TEST SCORES
====

The Programme for International Student Assessment (PISA) is a test given every three years to 15-year-old students from around the world to evaluate their performance in mathematics, reading, and science. This test provides a quantitative way to compare the performance of students from different parts of the world. In this homework assignment, we will predict the reading scores of students from the United States of America on the 2009 PISA exam.

The datasets [pisa2009train.csv](pisa2009train.csv) and [pisa2009test.csv](pisa2009test.csv) contain information about the demographics and schools for American students taking the exam, derived from [2009 PISA Public-Use Data Files](http://nces.ed.gov/pubsearch/pubsinfo.asp?pubid=2011038) distributed by the United States National Center for Education Statistics (NCES). While the datasets are not supposed to contain identifying information about students taking the test, by using the data you are bound by the [NCES data use agreement](https://d37djvu3ytnwxt.cloudfront.net/assets/courseware/8522b75c7463c923fb96ae274fdf8213/asset-v1:MITx+15.071x_3+1T2016+type@asset+block/NCES_Data_Use_Agreement.txt), which prohibits any attempt to determine the identity of any student in the datasets.

Each row in the datasets pisa2009train.csv and pisa2009test.csv represents one student taking the exam. The datasets have the following variables:

- **grade**: The grade in school of the student (most 15-year-olds in America are in 10th grade)

- **male**: Whether the student is male (1/0)

- **raceeth**: The race/ethnicity composite of the student

- **preschool**: Whether the student attended preschool (1/0)

- **expectBachelors**: Whether the student expects to obtain a bachelor's degree (1/0)

- **motherHS**: Whether the student's mother completed high school (1/0)

- **motherBachelors**: Whether the student's mother obtained a bachelor's degree (1/0)

- **motherWork**: Whether the student's mother has part-time or full-time work (1/0)

- **fatherHS**: Whether the student's father completed high school (1/0)

- **fatherBachelors**: Whether the student's father obtained a bachelor's degree (1/0)

- **fatherWork**: Whether the student's father has part-time or full-time work (1/0)

- **selfBornUS**: Whether the student was born in the United States of America (1/0)

- **motherBornUS**: Whether the student's mother was born in the United States of America (1/0)

- **fatherBornUS**: Whether the student's father was born in the United States of America (1/0)

- **englishAtHome**: Whether the student speaks English at home (1/0)

- **computerForSchoolwork**: Whether the student has access to a computer for schoolwork (1/0)

- **read30MinsADay**: Whether the student reads for pleasure for 30 minutes/day (1/0)

- **minutesPerWeekEnglish**: The number of minutes per week the student spend in English class

- **studentsInEnglish**: The number of students in this student's English class at school

- **schoolHasLibrary**: Whether this student's school has a library (1/0)

- **publicSchool**: Whether this student attends a public school (1/0)

- **urban**: Whether this student's school is in an urban area (1/0)

- **schoolSize**: The number of students in this student's school

- **readingScore**: The student's reading score, on a 1000-point scale
