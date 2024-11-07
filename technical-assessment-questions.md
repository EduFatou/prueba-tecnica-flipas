# Technical Assessment Questions

## 1. Website/Landing Page Project

I'm currently creating a website for a real client (an elderly residence) in ReactJS, using Bootstrap as style library, and deployed in Netlify. I thought that this is a perfect example for this subject. I'm collaborating with a friend who studied with me in the bootcamp. She is living in Peru, and we work by version control. We started only two days ago, it's still in process, but there is an mvp I can already show, even knowing that there are still loads of things to do to make it prettier, and we are waiting for the client to send us information and good pictures.

Here it is:

https://residenciaalarcon.netlify.app/

## 2. Short Answer Questions

- **Why do you want to work for Flipas?**

Well, that is a good question. Actually, I don't know why, but I couldn't get any information about Flipas. I guess that it's in stealth mode.
I like that. I would like to work in a team that knows how valuable are good ideas.

- **What draws you to computers and technology?**

I'm always fascinated with how computers work. I've been always surrounded by them, as I used to work as video editor, among others. Since I went full into developing I always feel the need to keep learning and mastering it. it's not only the intellectual challenge, but the reward of creating something useful.

- **What is your favorite aspect of programming?**

I think it's the creative problem-solving process. Getting gradually a solution for a problem is so rewarding.

- **If you couldn't work in tech, what career would you pursue?**

In this moment of my life, I forbid myself in thinking about something else. I'm fully committed to find a job as a developer. I know there is a place for me somewhere in this world.

## 3. Technical Problems

I didn't have time to answer them all, but I tried to get as many as possible, considering the time for the deadline:

### B. Tag Normalization

Here's how I would make the function for tag normalization, using Set() to get unique elements:

```typescript
function normalizeTags(tags: string[]): string[] {
    const uniqueTags = new Set(
      tags.map(tag => tag.toLowerCase())
    );
  
    return Array.from(uniqueTags)
      .map(tag => tag.charAt(0).toUpperCase() + tag.slice(1).toLowerCase())
      .sort();
 };
```

### D. Profile Search Component

I would create the component with a good data structure, indexing, adding pagination, caching, and debouncing to ensure that the component has a good performance, following this steps:

Assuming that we are working in Typescript, I would define the Profile type as below:

```typescript
type Profile = {
    id: string;
    name: string;
    labels: string[];
    location: string;
    lastActive: Date;
};
```

Then, I would create the Search parameters, the variables for indexing the profiles, and functions to add a Profile and store the indexed profiles in an array, so I can make a function that searchs and filters them by their keys.

After that, I would make the search function asynchronous, considering a large dataset.

Then I would create more functions for pagination, sorting results, debounce the search input, and for caching search results, using the local storage, and also monitoring the time needed via `console.log()`.

### E. Job Matching Algorithm

It uses a weighted scoring system for matching profiles by skills, experience, locatin and languages.

```typescript
type Skill = string;

interface UserProfile {
  skills: Skill[];
  jobPreferences: string[];
  experience: number;
  location: string;
  languages: string[];
}

interface MatchScore {
  profile: UserProfile;
  score: number;
  matchDetails: {
    skillsMatch: number;
    preferencesMatch: number;
    experienceMatch: number;
    locationMatch: number;
    languagesMatch: number;
  };
}

function calculateProfileMatches(
  targetProfile: UserProfile,
  potentialMatches: UserProfile[],
  weights = {
    skills: 0.3,
    preferences: 0.2,
    experience: 0.2,
    location: 0.15,
    languages: 0.15,
  }
): MatchScore[] {
  const weightSum = Object.values(weights).reduce((sum, weight) => sum + weight, 0);
  if (Math.abs(weightSum - 1) > 0.001) {
    throw new Error('Weights must sum to 1');
  }

  const matches = potentialMatches.map((profile) => {
    const skillsMatch = calculateSkillMatch(targetProfile.skills, profile.skills);

    const preferencesMatch = calculateArrayMatch(
      targetProfile.jobPreferences,
      profile.jobPreferences
    );

    const experienceMatch = calculateExperienceMatch(
      targetProfile.experience,
      profile.experience
    );

    const locationMatch = targetProfile.location === profile.location ? 1 : 0;

    const languagesMatch = calculateArrayMatch(
      targetProfile.languages,
      profile.languages
    );

    const score =
      skillsMatch * weights.skills +
      preferencesMatch * weights.preferences +
      experienceMatch * weights.experience +
      locationMatch * weights.location +
      languagesMatch * weights.languages;

    return {
      profile,
      score,
      matchDetails: {
        skillsMatch,
        preferencesMatch,
        experienceMatch,
        locationMatch,
        languagesMatch,
      },
    };
  });

  return matches.sort((a, b) => b.score - a.score);
}

function calculateSkillMatch(targetSkills: Skill[], profileSkills: Skill[]): number {
  const commonSkills = targetSkills.filter(skill => 
    profileSkills.includes(skill)
  );
  const uniqueSkills = new Set([...targetSkills, ...profileSkills]);
  return commonSkills.length / uniqueSkills.size;
}

function calculateArrayMatch(targetArray: string[], profileArray: string[]): number {
  const common = targetArray.filter(item => 
    profileArray.includes(item)
  );
  const unique = new Set([...targetArray, ...profileArray]);
  return common.length / unique.size;
}

function calculateExperienceMatch(targetExp: number, profileExp: number): number {
  const expDiff = Math.abs(targetExp - profileExp);
  if (expDiff <= 2) return 1;
  if (expDiff <= 5) return 0.5;
  return Math.max(0, 1 - expDiff / 10);
}
```