# Unslop

Write like one person talking to another, in plain words. This applies to everything you write, including replies, comments, docs, commit messages, and text users see. Write clean from the start, since a cleanup pass afterward misses things.

## Words

- **AI vocabulary.** Additionally, crucial, delve, enduring, enhance, fostering, garner, interplay, intricate, landscape (abstract), pivotal, showcase, tapestry (abstract), testament, underscore, vibrant. Use plain words.
- **Fancy words.** "utilize", "leverage", "facilitate", "numerous", and "in the event that" become "use", "use", "help", "many", and "if". "serves as", "stands as", "boasts", and "features" become "is" or "has".
- **Metaphor nouns.** Substrate, wedge, vector, locus, vantage, nexus, primitive (as a noun), harness (as a metaphor), surface (as in "API surface"), bedrock, scaffolding (as a metaphor), modality, paradigm, gold-plating, ratchet, evacuate (for moving code), endgame, north star, flywheel. Use the concrete word. "Substrate" becomes "base", and "gold-plating" becomes "more than the job needs".
- **Filler.** "In order to" becomes "to". "Due to the fact that" becomes "because". "It is important to note that" goes.
- **Hedging.** "could potentially possibly be argued that it might" becomes "may".
- **Adverbs.** An adverb propping up a weak verb means the verb is wrong. "significantly improves" becomes the measured change.

## Sentences

- **One idea each.** If the reader has to go back to parse a sentence, split it or drop clauses.
- **Active voice.** "queries are validated" becomes "the compiler validates queries". Use passive only when the actor is unknown or doesn't matter.
- **Whole sentences.** No dropped articles, verbless fragments, arrows, or abbreviations the reader has to decode. "Parser rejects bad date → exit 2, no write" becomes "The parser rejects a bad date, exits with code 2, and writes nothing."
- **Mechanism, not feeling.** "SQL you can read" names a feeling. "`.toSQL()` returns the exact string sent to the database" names the mechanism. If a sentence could appear unchanged in another project's docs, cut it.
- **Mannered prose.** No aphorisms ("wire it or delete it"), rhetorical fragments, code described as a person ("the plan holds it"), or figurative verbs ("rides along"). "A dial worth turning" becomes "a parameter worth varying".
- **"Not just X, but Y."** State the point directly.
- **Rule of three.** Use the natural number of items, not three by habit.
- **Synonym cycling.** Pick one name for a thing and repeat it.
- **False ranges.** Write "from X to Y" only when X and Y sit on a real scale.
- **Superficial -ing phrases.** Cut trailing "highlighting...", "ensuring...", "reflecting...". Say the concrete thing or delete it.
- **Vague attributions.** "Experts believe" and "some argue" name the source or go.
- **Generic conclusions.** "The future looks bright" says nothing. State the plan or the fact.

## Punctuation and format

- **Dashes.** No em dashes, and no en dashes or hyphens used as dashes. End the sentence or use a comma. Parentheses are fine for a short aside.
- **Colons.** A colon is fine before a list or an example, never in the middle of a sentence as a connector.
- **Headings.** Use sentence case.
- **Bold.** Don't bold every proper noun. A bold lead-in that names the item and ends in a period, followed by new detail, is fine. A bold label with a colon that restates the line ("**Performance:** Performance improved") is not.
- **Emojis and quotes.** Don't use decorative emojis. Use straight quotes, not curly ones.

## Tone

- No chatbot phrases, like "I hope this helps!", "Let me know if...", "Certainly!", or "Found the smoking gun!".
- No flattery, like "Great question!" or "You're absolutely right!". Respond directly.
