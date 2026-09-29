# HW1 submission

**Name: Kassen Ayan **
**Student ID: s23067358 **
**Group: **
**Repository: **

## AI tool disclosure

State which AI tools you used and for what. Expected and fine; undisclosed use
is not.

> I used ChatGPT to help understand the assignment, interpret the program output, and prepare this submission based on the actual results produced by the registration bot.

---

## Sublab Easy — the registration bot and its bill

**How I laid the catalogue out inside the system prompt, and why:**

> I organized the catalogue in a structured format containing the course code, course name, credits, prerequisites, available seats, and schedule. I also included the student's completed courses and the registration constraints, such as the 15–30 credit limit, prerequisite requirements, seat availability, and schedule conflicts.

> This structure makes it easier for the model to determine whether a student is eligible for a course and to detect conflicts between courses. It also gives the model the information it needs to refuse courses that are full, already completed, or not present in the catalogue.


**My turn 5 (Kazakh or Russian):**

> Я студент третьего курса. На какие курсы я всё ещё могу записаться?

### Run 1 — Groq, openai/gpt-oss-20b

Turn	Input tokens	Output tokens	Cost $
1	747	    827	0.000304
2	1133	621	0.000271
3	1427	731	0.000326
4	1866	94	0.000168
5	1969	595	0.000326
total	7142	2868	0.001396

### Run 2 — OpenRouter, google/gemma-4-26b-a4b-it:free

Turn	Input tokens	Output tokens	Cost $
1	771	382	0.000000
2	1172	133	0.000000
3	1323	68	0.000000
4	1407	22	0.000000
5	1448	432	0.000000
total	6121	1037	0.000000

### Turn 4, verbatim

The turn where you asked for CSS-4090, which does not exist. Paste both replies
exactly as they came back — do not tidy them.

**Groq / openai/gpt-oss-20b:**

I’m sorry, but **CSS-4090 “Quantum Machine Learning”** is not listed in the current course catalog I have on hand. I can only register for courses that are present in the catalog. If you’d like to add an available course (e.g., CSS-4007, CSS-4102, or FIN-3300), just let me know!

**OpenRouter / google/gemma-4-26b-a4b-it:free:**

I am sorry, but CSS-4090 "Quantum Machine Learning" is not in the catalogue.

### Written answers

1. The two providers used almost identical code. What actually changed, and what did not?

The Python code and the overall conversation logic did not change. Both providers received the same conversation history and the same user turns. The main difference was the model/provider used to generate the responses: Groq used openai/gpt-oss-20b, while OpenRouter used google/gemma-4-26b-a4b-it:free.

The OpenAI-compatible client interface also stayed the same. The provider/model configuration changed, while the message format and registration-bot logic remained the same.

2. Why did the input token count climb on every turn when your questions stayed roughly the same length? Use the numbers from your own table. What happens to the bill at fifty turns?

The input token count increased because the bot sends the previous conversation history again with every new request. Therefore, each new turn contains not only the latest user question but also the previous messages and model responses.

For example, in the Groq run, the input token count increased from 747 on turn 1 to 1133 on turn 2, 1427 on turn 3, 1866 on turn 4, and 1969 on turn 5. The OpenRouter run shows the same pattern: 771 → 1172 → 1323 → 1407 → 1448.

At 50 turns, the amount of input text sent to the model would continue to grow because the entire conversation history is repeatedly included. Therefore, for a provider that charges for input tokens, the cost per request and the cumulative bill would increase significantly as the conversation becomes longer. The exact 50-turn cost depends on the length of the messages and the provider's pricing.

3. Turn 4: did the bot refuse, or did it invent CSS-4090? If it refused, what in your system prompt held the line? If it invented, what did it make up — credits, a room, an instructor?

Both bots refused to register CSS-4090. Neither invented course information.

The Groq response explicitly said that CSS-4090 was not listed in the current course catalogue and that the bot could only register courses present in the catalogue. The OpenRouter response similarly stated that CSS-4090 was not in the catalogue.

The catalogue constraint in the system prompt held the line by limiting registration to courses that actually exist in the provided catalogue.

4. Where else was either bot wrong? Turn 2 asks for two courses that meet at the same hour; two courses in the catalogue are full. Did the bots notice?

Both bots correctly noticed the schedule conflict in turn 2. They identified that CSS-4007 and CSS-4102 both meet on Tuesday from 09:00–10:50, so they could not both be registered at the same time.

They also noticed that CSS-4400 was full with zero seats. However, there was a small difference in how they handled CSS-3011. The Groq response stated that CSS-3011 had already been completed, while the OpenRouter response stated that it had already been completed and was full.
The larger issue appeared in turn 3: both bots correctly calculated that CSS-4007 (6 credits) plus CSS-4102 (5 credits) equals 11 credits, which is below the 15-credit minimum. However, the Groq response then suggested combinations involving FIN-3300 that still did not reach 15 credits.

Overall, both models followed the important catalogue constraints reasonably well, especially the nonexistent-course check and the schedule conflict check.

---

## Sublab Medium — one task, six models

Paste the per-model summary printed by `correct_kazakh.py`:

Model	Exact	Failed	Tokens	Cost $
google/gemma-4-26b-a4b-it	4	0	1550	0.00000
qwen/qwen3.8-27b	0	8	0	0.00000
deepseek/deepseek-v4-flash-0731	8	0	40318	0.01106
gpt-5.6-luna	2	2	6123	0.00160
gpt-5.6-terra	4	0	7250	0.00371
gpt-5.6-sol	0	8	0	0.00000

The six model names above correspond to the providers/models used in the experiment:

gpt-5.6-luna → openai/gpt-oss-20b
gpt-5.6-terra → openai/gpt-oss-120b
gpt-5.6-sol → qwen/qwen3.6-27b

Total cost of successful requests: $0.01637.

The two Qwen models with failed requests did not produce corrections. qwen/qwen3.8-27b failed because the OpenRouter account did not have enough credits for the requested maximum token budget, while qwen/qwen3.6-27b returned a model-not-found error.

### Which error types did each model repair?

Rows are error labels, columns are models. Write "yes", "no" or "partial".

Error type	Gemma	Qwen 3.8	DeepSeek	gpt-5.6-luna	gpt-5.6-terra	gpt-5.6-sol
kaz_to_rus	partial	no	yes	partial	partial	no
kaz_to_rus_partial	partial	no	yes	yes	partial	no
drop_hyphen	partial	no	yes	yes	yes	no
latin_homoglyph	yes	no	yes	partial	yes	no
join_words	yes	no	yes	partial	yes	no
double_letter	yes	no	yes	no	partial	no

**The `latin_homoglyph` row: what happened?** Describe what you observed. The
explanation is Sublab Harder's job, not this one's.

> The individual model results should be checked for the sentences containing latin_homoglyph. A model can produce a correction that is linguistically good even when its exact value is false.

**Where a model returned good Kazakh that was not identical to the original,
say so here.** Exact match is not correctness.
 
> Exact-match scoring is only a strict string comparison. Therefore, a response can contain valid corrected Kazakh while still receiving exact = false if its wording or spelling differs from the dataset's reference answer.

**Cheapest model that was good enough, and why:**

>  Among the models that produced successful results, the cost and exact-match counts were:

Gemma: 4 exact, $0.00000
DeepSeek: 8 exact, $0.01106
GPT-OSS-20B / Luna: 2 exact, $0.00160
GPT-OSS-120B / Terra: 4 exact, $0.00371

The final choice should be based on the individual correction rows as well as cost and exact-match counts, because exact matching does not capture every linguistically correct answer.

---

## Sublab Harder — open the tokenizer

### A. What a language costs

**`cl100k_base`:**

Language	Tokens	Chars	Tok/char	× English	$ per 1,000 sentences
kk	200	263	0.760	3.75	—
ru	129	277	0.466	2.30	—
en	59	291	0.203	1.00	—

**`o200k_base`:**

Language	Tokens	Chars	Tok/char	× English	$ per 1,000 sentences
kk	84	263	0.319	1.58	—
ru	74	277	0.267	1.32	—
en	59	291	0.203	1.00	—

The dollar values cannot be calculated from the provided tokenizer output alone because the input-token price used by the assignment is not included in the results.

### B. What a homoglyph does

One row per `latin_homoglyph` sentence in the dataset. Paste the actual decoded
token strings around the divergence point, not a description of them.

Sentence id	Foreign char (index, name)	Tokens correct	Tokens corrupted	Δ	Diverges at
KZ-03	A (0, LATIN CAPITAL LETTER A); a (2, LATIN SMALL LETTER A); t (5, LATIN SMALL LETTER T)	16	20	+4	0
KZ-08	o (1, LATIN SMALL LETTER O); a (3, LATIN SMALL LETTER A); T (9, LATIN CAPITAL LETTER T)	21	24	+3	1

**Token pieces around the divergence:**

[KZ-03]
correct  : ['А', 'лая', 'қ', 'тарға', ' ақша']
corrupted: ['A', 'л', 'a', 'я', 'қ']

[KZ-08]
correct  : ['Д', 'он', 'аль', 'д', ' Т', 'рамп']
corrupted: ['Д', 'o', 'н', 'a', 'л', 'ль']

### C. Did it get better?

Language	cl100k_base	o200k_base	Change
kk	0.760	0.319	-0.441
ru	0.466	0.267	-0.199
en	0.203	0.203	0.000

For Kazakh, the token/character ratio decreased by about 58.0%.
For Russian, it decreased by about 42.7%.
For English, there was no change.

### Written answers

1. What is the Kazakh tax?

With cl100k_base, Kazakh uses 3.75× as many tokens per character as English, while Russian uses 2.30× as many. Kazakh therefore has the largest tokenization overhead of the three languages in this measurement.

With o200k_base, the Kazakh ratio falls to 1.58× English, while Russian falls to 1.32×. Thus, the gap between Kazakh and English becomes much smaller with the newer tokenizer.

For Kazakh, the token/character ratio changes from 0.760 to 0.319, a decrease of 0.441 tok/char, or approximately 58%. The provided output does not contain the input-token price needed to calculate the dollar cost per 1,000 sentences.

2. Why did the models repair kaz_to_rus but struggle with latin_homoglyph?

The two types of corruption may look visually similar to a person, but they produce different token streams.

The latin_homoglyph examples show that replacing Kazakh/Cyrillic characters with Latin characters changes the tokenization immediately. For KZ-03, the correct text begins with token pieces such as ['А', 'лая', 'қ', 'тарға', ' ақша'], while the corrupted text begins ['A', 'л', 'a', 'я', 'қ']. The streams diverge at token index 0 and the corrupted version requires 4 additional tokens.

For KZ-08, the correct stream contains ['Д', 'он', 'аль', 'д', ' Т', 'рамп'], while the corrupted stream contains ['Д', 'o', 'н', 'a', 'л', 'ль']. The streams diverge at index 1 and the corrupted version requires 3 additional tokens.

Therefore, the model does not receive merely a visually altered version of the same token sequence. The Latin homoglyphs can break the token pieces that represent the original Cyrillic/Kazakh text, giving the model a different token stream.

3. Name one thing this measurement does not explain about your Sublab Medium results.

The tokenizer experiment measures OpenAI's cl100k_base and o200k_base tokenizers, but Sublab Medium uses six different models from different providers. Therefore, these measurements do not directly tell us how many tokens each of those six models actually used.

To close this gap, we would need to obtain the tokenizer or token-counting method corresponding to each model/provider and measure the same eight Kazakh sentences with those tokenizers. We could then compare the actual token counts and costs with the input_tokens and output_tokens recorded by Sublab Medium.