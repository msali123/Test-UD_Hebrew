# IAHLT UD Guideline Recommendations
These guidelines are meant to supplement and ultimately be merged with the general UD Hebrew guidelines. Annotation decisions are based on general principles and compatibility with similar languages (esp. Arabic), though all other things being equal, backwards compatibility with UD_Hebrew-HTB is prioritized (for example in keeping together lexicalized complex adverbials, such as היום/ADV).

## General principles
### Lemma
Lemmas should be provided in their non-vocalized form (כתיב מלא).
In case that there is more than one commonly-used spelling for a word – For example, words that originate from other languages and can be written with or without the letter אל"ף (e.g. מטאפורה  vs מטפורה) – please provide the form that is more frequent (use Google search to determine in case you are not sure).

### Removal of reconstructed articles and של/את   
We recommend completely disregarding reconstructed articles and possessives, which in UD_Hebrew-HTB lead to the insertion of a pseudo-token with the word form _של_ in possessives such as:
הזמנה_ + _של_+_הם → הזמנת+ם
And articles fused with prepositions as in:
 ב_+_ה_+_בית → ב+בית

This keeps the data closer to the analysis in Arabic treebanks and makes use of general purpose tools easier, as well as allowing any additional annotations to be aligned to the base text, while avoiding blatant tokenization errors which disrupt downstream usage.
To keep this modification trivial and bidirectionally reversible, we add a feature Definite=Def on prepositions preceding fused articles. The possessive is trivially reversible since it is the only way in which a PRON token is dominated as nmod:poss without the preposition של.

### Elliptical promotion
In keeping with current UD guidelines, functional elements are promoted to the function of their elided parent, such as promoting prepositions without an object to take the role of nmod/obl, as well as subtypes such as nmod:poss, as required. For example in the following של is not labeled as ‘case’:
 
 Tree
 
Promotion can generally lead to POS tags carrying unusual dependency relations, for example a VERB can be promoted to substitute a missing NOUN that would control it, resulting in VERBs taking relations such as obj. For example in the following analysis:

Tree


The missing word מה should be the object of לתאר (as in לתאר את מה שמתרחש). Because UD prioritizes preserving the argument structure of the matrix clause (and more generally, of whichever structure is ‘higher’ in the tree), the VERB מתרחש stands in for the obj function from the argument structure of לתאר, and will also be the promoted head dominating the case relation for את, but it will still be tagged VERB. For more discussion of this example, see Free relatives below.

## Constructional changes from UD HTB
The following analyses differ from UD HTB for the reasons listed below, and are implemented in the new sample data.

### Clausal subjects
The current analysis of סביר as an auxiliary in HTB looks wrong and should probably be revised, since it is actually the predicate, not an auxiliary, and the ש clause is not really an advcl:
HTB analysis with סביר as AUX:

tree

Suggested analysis used in new data with סביר as ADJ:

tree

### Infinitives marked by ‘prepositions’
Constructions like the following should be mark, not case, since the function word introduces a clause (in this case, infinitival):
HTB:

tree

Suggestion:

tree

### Adverbial ‘auxiliaries’
Current UD guidelines and the UD validator prohibit the deprel advmod for words not tagged ADV; HTB has אינו + VERB tagged as AUX + advmod. I recommend changing this to deprel aux with Polarity=Neg:

tree

### Additional function words
The following function words were encountered in sample data which were not found in HTB, for which we recommend these guidelines:
-	 לכש – leave as one token
-	ה|די משמעותי (definite הדי before + ADJ) - split הדי, analyzed as the article attached to the adjective, not to די, with די as an adverb to the ADJ.

### Free relatives
The original HTB had no consistent analysis for free relatives, in which an interrogative pronoun fulfills the role of both a matrix clause and a subordinate clause argument. The standard practice from other language UD TBs is to consistently prefer the matrix clause function and attachment, attaching the subordinate predicate as acl:relcl, and reserving the subordinate function and attachment for future annotation via enhanced dependencies as desired (as implemented e.g. in the UD English treebanks).

For example, simple dependencies for מה, which is obj of both the matrix clause and the subordinate clause in the following example, should be annotated as follows:

tree

Note that in this analysis, the internal dependency of מה as the object of אומר is neglected, though it could be represented in enhanced dependencies, if these were to be added to the Hebrew treebank data.

In rare cases, the free relative analysis cannot be expressed via the relation acl:relcl due to ellipsis. Consider the following example:

אין לנו דרך טובה יותר לתאר את שמתרחש בבית החינוך במכללת אורנים

In a “full” realization, this could be re-formulated with “מה שמתרחש”, resulting in the same analysis as above, with acl:relcl from מה to מתרחש. However, because מה is missing, we must apply a promotion analysis. Since UD prefers preserving the matrix argument structure over embedded structures in the simple dependencies, the priority is given to preserving the argument structure of לתאר, which should take a direct object. As a result, מתרחש must inherit the obj function and case dependent of the missing מה, resulting in the following analysis:

tree

### Determiners should not be tagged as ADV
UD validation prohibits the function det on words tagged anything but PRON and DET. Therefore we should tag עוד חברה and יותר חברות as DET.

### Adverbially used NPs
Because of the UD prohibition on the relation advmod for tokens not tagged as ADV (or part of a multiword fixed expression), adverbially used NPs are attached as obl/nmod as appropriate, depending on the POS of the parent, even though they do not carry prepositions:

### Tagging of names and internal modifiers

HTB is not consistent in tagging proper names which consist of otherwise common nouns, though it consistently avoids PROPN on non-noun parts of names. I recommend generalizing this practice for consistency, for example:

tree

HTB regularly tags acronyms such as צה”ל as PROPN, but in this example it seems reasonable that חיל ההנדסה הקרבית also functions as a name. In keeping with HTB practices, קרבית and the function words within the name are tagged as usual, while the noun parts are tagged PROPN.
An exception to this guidelines is the consistent treatment of common noun titles in HTB as NOUN, which we have kept in newly tagged data as well, for example:

tree


Note that this practice is not consistent with UD English data (Mr./PROPN, General/PROPN), but is consistent with UD Arabic data (الشيخ and similar). Words not used as common nouns, however, are consistently tagged as PROPN, for example רבי (as a final-stressed name component, like רבי עקיבא). This appears at least superficially inconsistent, and we may want to change this.

### Dates and nummod

Unlike the original HTB practice of denoting numerical temporal modifiers as nummod, we attach years to month names, or in full dates to the day, since nothing is really being counted, cf. UD English GUM). 

tree

### Auxiliaries and negation
Auxiliaries should be listed somewhere. They currently include:
●	 יכול
●	 אמור
●	 אפשר
●	 צפוי
●	 ניתן
●	...
One issue arising with auxiliaries is handling special negation, such as אי אפשר. This is currently handled with אי attached as compound:affix to אפשר, but this is invalid according to UD guidelines and validation scripts, which prohibit dependents of tokens labeled aux.
The two possibilities are either treating it as a fixed expression, or attaching the negation to the main verb. Although it is morphosyntactically counterintuitive, I recommend the second option, since it avoids a multiword fixed auxiliary (creates problems for the language-specific list of auxiliaries in the validator), and streamlines the handling of complex predicate negation (looks exactly like standard לא, e.g. לא ניתן לעשות). This is generally the handling of negated modal auxiliaries across UD.


-	Current analysis: (invalid)

tree

-	Solution 1: (valid according to guidelines, but incompatible with current UD validator)

tree

-	Solution 2: (my choice, equivalent to current UD English analysis of can’t + VERB)

tree

Here are some cases where אי אפשר is not followed by INF. 
First, we have a case of a subordinate clause. So following your recommendation, I’m assuming that we have the same analysis of אי אפשר modifying לעמוד, and then לא is modifying it, too: a double negation, rehteorically it is a litotes. 
אי אפשר שלא לעמוד בפיתויים החושיים שמציעים לנו מטבחים ואין על כך שום פלא.
By analogy to the English treatment, both ‘i and efshar, as functional elements, would modify laamod.

And this? 
אי אפשר , כמובן , בלי כמה משפטים על ליאונל מסי וארגנטינה .
This is different: the sense is i efshar plus an elided infinitive (i efshar laasot X bli …), so efshar would be promoted to the head, bli kama mishpatim is obl to that, and ‘i is a simple negation to efshar, the root. See promotion above, and here.

Same here? אי negates יהיה? 
אבל מה לעשות , דווקא אצלכם , אי אפשר יהיה ( לפחות בעתיד הנראה לעין ) להעביר את כל הענין לרשת .
No, yihiye is also AUX, so they all modify lehaavir (incl. ‘i and efshar separately)

Are the following elliptical? How would you analyze them? 
Can this be an advmod modifying יותר? 
לכולנו יש רגעים שבהם אי אפשר יותר .
Efshar would be promoted to the head of the acl:relcl, all words in the relative clause would depend on it.

This is an obvious case of ellipsis. How do we go about it? 
הגוף מפנה הרבה משאבים כדי לייצר חיים חדשים וזו לא תקופה טובה כ"כ להעמיס יותר , גם אם אח"כ אי אפשר.
The same, efshar is promoted to head of the advcl. In enhanced dependencies you could add an ellipsis token to represent the missing infinitive, and in edeps efshar would be aux to it.
אי אפשר has a different function here, and I suspect we are going to get more creatures like that when we work on the language variety of the social media, where phrases such as אין דברים כאלה and the like are ubiquitous. Perhaps it’s premature, but we may want to start thinking about solving these harder cases. 
בלי עורכים ומתרגמים אי אפשר , אז קבלו את יעל סלע שפירו .
Perhaps I’m biased to expecting an infinitive argument, but again I would promote efshar here. Otherwise you would get different structures when you have a VERB and also the bli phrase, which seems less consistent. In English, where modals are expected to take infinitives, the modal would be head of its clause and dominate the PP as obl, for example:

https://corpling.uis.georgetown.edu/annis/?id=afc52611-284d-4b80-b5ca-500fa3df16df



### Functions of גם
When attached to a single NP, גם is currently tagged ADV but attached as det. I propose attaching it as advmod, as done in other languages. For the structure גם ... וגם I recommend using two cc attachments, again in keeping with guidelines for many other languages (both X and Y, etc.).

tree

## Other Recommendations (open)

The following recommendations have not yet been implemented in the new sample data, and may require more discussion and/or manual intervention and are left for future work:

### Removal of some relation subtypes 
UD_Hebrew-HTB uses a large number of dependency relation subtypes not found in other treebanks. I would recommend removing the following based on the argumentation below:
-	Case subtypes: labels such as case:gen and case:acc for של and את respectively seem redundant, since there are already morphological features for case, and they are deterministically predictable from the lemma and function. 
-	The nsubj:cop subtype: copular subjects are not generally distinguished in other UD treebanks, and the presence of a copula, or other non-verbal predication, can be read directly from the graph
-	The subtype flat:name is the only type of flat used in the corpus, therefore it would make sense to change it to simple flat. You don’t expect other “flats”? 
-	The subtype mark:q is used for האם and שמא, even when not used in an actual question. Since we have the word form/lemma and function mark as a deterministic predictor of this relation, I think it should be removed and simplified to mark. The item האם is also tagged ADV, but I think it should be SCONJ.
-	HTB has no passive labels – we added nsubj:pass and csubj:pass for passive subjects (useful for argument structure extraction, common in many TBs, incl. Arabic)

### Adding missing features
-	Polarity=Neg is used sporadically in HTB, but probably should be used consistently for all negative items, including לא, אי, בלתי, אין etc.
-	Typo=Yes is not currently used but likely to be needed for User Generated Content
-	Poss=Yes is not currently used for fused possessive pronouns, but should probably be added and this can be automated easily.


## Segmentation
### Complex expressions NOT to segment
The following expressions are not segmented and retrained as single tokens:

-	היום, החודש, השנה, השבוע (in the temporal adverbial sense "this year" etc)
-	בדיוק, ביותר, בלבד, בעיקר, בכלל, בפרט
-	לפחות, למשל, לעיתים, לשעבר
-	כיום, כמעט, כמובן, כנראה

### Similar complex expressions that ARE segmented
-	כרגיל, כראוי

## POS Tagging in Detail
This section goes over the Universal POS tags and their assignment for Hebrew text, taking into account UD HTB practices and guidelines for data from the Web, and in particular User Generated Content. For confusing cases, consult the subsections of Confusing Cases, which are headed “TAG1 or TAG2” in alphabetical order of tags (e.g. NOUN or VERB). 

Note that some categories are closed class, meaning that no words should receive those tags if they are not already attested with these tags in gold data. Lists of words within closed classes should be kept up to date if new words are ever added when encountering new cases in text.

### Open classes
#### ADJ

The ADJ category encompasses words with variable gender and number which may modify nouns as adjectival modifiers (see deprel amod) or stand as predicates. In both cases, they are inflected to match the described entity.

The ADJ category inflects for the morphological feature categories Gender and Number.

Tests for being an adjective include:
-	Inflection: גדול/גדולה/גדולים/גדולות
-	Definiteness agreement: ה בית ה גדול (the word גדול agrees in definiteness -> ADJ)
-	Comparison: יותר גדול

Typical adjectival formations appear in the pattern qatil (e.g. בהיר) and by formation with the suffix -י often from nominal stems (e.g. חתולי). 

Compound adjective bases are also tagged ADJ and take a dependent with the deprel compound, for example:

חסר/ADJ אחריות/NOUN

For adjectival/adverbial prefixes such as בלתי, תת see compound:affix.

#### ADV
The tag ADV is given to oblique modifiers of VERB and intensifiers of ADJ tokens when the ADV head is not modified by a preposition (otherwise, see NOUN and the deprel obl). These modifiers typically specify spatio-temporal, manner and purpose for VERB parents (אז, שם, כך, לפיכך), and degree (מאוד, פחות) for ADJ parents. Only ADV tokens (or equivalent multiword expressions chained via the deprel fixed) may carry the advmod deprel.

This class includes untokenized fixed prepositional phrases and definite adverbials, such as היום, לעיתים etc. which are treated as one token in segmentation. Note that similar expressions which are segmented would not be labeled ADV, but rather receive a compositional analysis as NOUN, e.g.
ב/ADP מהירות/NOUN

Negation is also treated as an adverbial modification, e.g. 
לא/ADV שמעתי/VERB

The most common Hebrew adverbs include:

לא, גם, יותר, רק, אולי, אתמול,שם, אז, כך, ביותר, כבר,דווקא

For adjectival/adverbial prefixes such as בלתי, תת see compound:affix.

#### INTJ

The tag INTJ is given to interjections, interpreted fairly broadly: this category is used for exclamative sounds and hesitation markers (אה), as well as pleasantries or insults that do not saturate a syntactic participant role in the sentence (e.g. בבקשה, חרא). The same items are typically attached via the deprel discourse. 

Particles accompanying vocatives and imperatives also fall into this category, e.g. 

נא/INTJ לבוא/VERB
הו/INTJ שוטה/NOUN 

#### NOUN

The tag NOUN is given to all common nouns, including abstract and concrete references to people, places, things and ideas, as well as deverbal nouns (שמות פעולה). Note that proper nouns (i.e. names) are given the separate tag PROPN. 

NOUNs carry the Gender and Number morphological features. Nominalized participles (בינוני) which carry an article or serve an argument structure role as a participant in a predication are tagged NOUN as well, whereas predicate participles are tagged as VERB (see also NOUN or VERB?).

#### NUM

The NUM tag is given to numerals, whether spelled in Arabic numerals or as words, as well as other types, such as Roman numerals etc. Note that ordinals do not constitute NUM, but are typically tagged as ADJ (e.g. ראשון, שני).


#### PROPN

The tag PROPN is given to proper nouns, which are nouns referring to named, typically uniquely identifiable entities, such as דינה, גוגל, צרפת. 
Note that in the original HTB, transparent common noun components of institutional names are tagged NOUN, e.g. 

אוניברסיטת/NOUN בוסטון/PROPN

We revise this to:
אוניברסיטת/PROPN בוסטון/PROPN

Note that lemmatization does revert transparent morphological inflection even on PROPNs, meaning that the lemma will be אוניברסיטה, not אוניברסיטת.

More generally, within each span of tokens designating a name, any token that is otherwise a NOUN will be tagged PROPN, but all other POS tags are retained unchanged. More examples:

-	רמת/PROPN גן/PROPN
-	קיבוץ/NOUN עין/PROPN ה/DET שלושה/NUM

Function words inside names are tagged as usual (not PROPN). Dependency relations for syntactically transparent name constructions are analyzed compositionally (for names with opaque syntax, see flat).


#### VERB

The tag VERB is given to morphological verbs in Hebrew which can express tense, including participles when used as a present tense form (see NOUN or VERB?). It does not include a specific subset of auxiliaries (see AUX) or verbs used as a copula (see COP). It additionally includes some non-morphological predicate forms listed below.

Normal tensed verbs carry the morphological features Tense, Voice, Number, Gender, Mood, VerbForm and Person. Additionally, the Hebrew-specific category HebBinyan expresses the morphological paradigm that a verb belongs to, using one of the seven traditional pattern names (e.g. PAAL, PIEL, etc., see HebBinyan).

Modal and copula verbs are assigned with the morphological features VerbType=Mod and VerbType=Cop, respectively.
 
In addition to these, the following items are tagged as VERB:

-	Absolute existentials יש, אין and inflected forms ישנו, איננה etc.
-	The otherwise auxiliary modals (see AUX) when used as a main predicate, i.e. when they are not auxiliary to anything else. For example:

tree

### Closed classes
Note: UD also uses a tag PART for particles, which is not currently in use in UD HTB. If we want to use this tag, we can consider adding guidelines for it.

#### ADP

The tag ADP is used for adpositions, which in Hebrew designates either simple (i.e. single token) prepositions or complex (multi-token) ones, whose components are attached to the first word in the complex expression. The preposition is attached to the head of its phrase with the deprel case. The following list is based on HTB’s contents and is intended to be exhaustive, though further prepositions which are not listed yet can potentially be added after careful consideration.

List of single token prepositions:

| אחר     | בטרם   | בתוככ |
|---------|--------|-------|
| אחרי    | בידי   | דרך   |
| אל      | בין    | החל   |
| אצל     | בלי    | כ     |
| את      | במשך   | כגון  |
| ב       | בעקבות | כדי   |
| באוזני  | בפני   | כלפי  |
| באמצעות | בקרב   | כמו   |
| כנגד    | לפני   | מפני  |
| ל       | לרגל   | מתוך  |
| לאור    | לשם    | מתחת  |
| לאחר    | לתוך   | נגד   |
| לבין    | מ      | עד    |
| ליד     | מאשר   | על    |
| ללא     | מבין   | עם    |
| למרות   | מבלי   | של    |
| לעבר    | מול    | תוך   |
| לעומת   | מן     |       |
| לפי     | מעבר   |       |


In complex prepositions, components derived from other POS tags retain their original tags, while the entire expression is attached using the deprel case.

tree

#### AUX

The AUX category is used for four main kinds of words: verbal copulas attached with the deprel cop, tense auxiliaries, assertive auxiliaries (negation with אינו), and modals attached to a lexical predicate with the deprel aux. Note that independent modals (e.g. צריך+NOUN) are not tagged as AUX, but as VERB. Pronominal copulas should be tagged PRON.

Typical copula cases follow this pattern:

tree

Tense auxiliaries include forms of היה forming a past progressive or counterfactual conditional (irrealis) together with a participle, for example:

tree

Assertion auxiliaries of the type אינו\איננו are analyzed as follows

tree

Finally, modal auxiliaries include items such as יכול, צריך, רוצה etc. which modify a lexical head VERB to indicate modality. Note that in keeping with other UD treebanks, impersonal modals with subject infinitive clauses are analyzed differently (אפשר, אסור and also צריך without an experiencer subject); see the deprel csubj for more details and examples.

tree

The following lemmas may be AUX:
-	יכול, צריך, חייב, מוכרח, מסוגל
-	אינו, הינו, היה, אין, יש
-	מוכן, רשאי, זכאי, צפוי
-	עשוי, עתיד, אמור, עלול

#### CCONJ

This tag is given to coordinating conjunctions, most commonly ו, או, אבל and similar words. The first conjunct governs all subsequent ones as conj, while the coordination is governed by its immediate successor head as cc and given the POS tag CCONJ.

tree

The following lemmas may be tagged as CCONJ:

-	אך, אבל, אולם
-	או, ו
-	אלא (לא X אלא Y)

#### DET

The tag DET is given to determiners which specify (in)definiteness (ה, כמה) or quantification (כל, הרבה) for a single noun phrase, usually attached as det. The following lemmas may serve as DET:
-	ה
-	כל, כמה, מִדֵי ('מִדֵי יום')
-	רוב, שאר, הרבה 
-	אף, שום
-	מספיק, מספר

Note that those items which can appear in construct or absolute state, the absolute state rendition implies separate noun phrases for the quantifier and the quantified phrase. In keeping with other corpora, DET items are still tagged DET in such cases, but are no longer given the deprel det, i.e. הרבה תלמידים is det, but הרבה מהתלמידים is regular PP modification (nmod). In both cases, הרבה is tagged DET.

#### PRON

The PRON tag is given to several kinds of pronouns, including personal pronouns (אני, הן), demonstrative pronouns (זה, אלו), interrogative pronouns (מי, מה) and copula pronouns (א הוא ב). A complete list of lemmas which are allowed to take the PRON tag:

-	הוא (incl. forms אני, את, הם…)
-	זה
-	כך
-	עצמו (note: not segmented, per HTB conventions)
-	אותו (unsegmented and tagged PRON only in the sense the same one)
-	זהו
-	מי
-	מה
-	כלשהו
-	איזה

Note that the word כךis tagged as PRON only when it is accompanied by a preposition; when appearing by itself, it is tagged ADV. The pronominal adverbs, incl. Interrogatives, are tagged ADV (איכשהו, איפשהו, מתי, איך, איפה)

Copula pronouns are tagged PRON as usual and given the deprel cop:

tree

Pronouns carry Gender, Number, Person (where relevant), and PronType morphological features.

#### SCONJ

The tag SCONJ is given to subordinating conjunctions which mark the function of the clause they introduce, including adverbial clauses, direct speech and relative clauses. Note that this includes uninflected relative markers (ש, אשר) and the article-like conjunction ה when introducing a relative clause, typically with a participial (beinoni) modifier, for example:

tree

Note that the main criterion for identifying this construction, as opposed to an ADJ with an agreeing article (ה as DET and not SCONJ), is the substitutability with ש - for example in:

-	כל הדרישות החשובות לכם

The embedded predicate חשובות is not a VERB, but we still attach it as acl:relcl, and tag ה/SCONJ, since it is equivalent to “כל הדרישות שחשובות לכם”. That said, in some archaic/elliptical constructions, substitution with ש is not possible and we still assign SCONJ to ה, specifically the “whosoever” type construction in:

-	כל המציל נפש בישראל… (=כל מי ש)
-	הבנה בסיסית של המתרחש (=מהשמתרחש)


The following simple lemmas are allowed as SCONJ:
-	ש, אשר, ה
-	כי, אם
-	כפי, ככל, כשם
-	אם, שמא
-	כש, לכש, כאשר
-	משום, כיוון, מכיוון

Note that in multiword expressions serving as SCONJ, constituent tokens will receive their regular POS tags, though they will be attached to the first token in the SCONJ expression with the deprel fixed.

tree

### Special symbols and other cases
#### PUNCT

The tag PUNCT is used for non-alphabetic tokens which delimit text, such as commas, parentheses, periods, etc. Similar symbols which are pronounced, such as currency symbols, hyphens meaning ‘to’ (between numbers), measurement units, etc. are not PUNCT, but SYM.

#### SYM
The tag SYM is given to non-alphabetic tokens which are pronounced and serve some function in the sentence, for example hyphens meaning ‘to’ (between numbers), currency symbols, etc.

#### X

The tag X is used for words that cannot be assigned a real or ‘linguistic’ part-of-speech category, either because we do not know it (garbled text, gibberish), or because it is not in Hebrew. The latter category encompasses only cases of true “code switching” or foreign text cited verbatim within Hebrew data; it does not include syntactically integrated use of loan words.

### Confusing cases
#### ADJ or NOUN?

Nouns are sometimes used within adjective-like constructions, such as comparison. For such cases we retain the NOUN tag based on the underlying morphological category of the word, rather than the syntactix usage:

מי יותר גבר/NOUN?

For uncertain NOUN/ADJ cases in which a possible noun exhibits gendered forms

Specific lemmas which are conventionally analyzed as ADJ even when not used as amod include: אחר, קיים

#### AUX or VERB?

Strong uses of היה to indicate pure existence or possession are tagged VERB, not AUX (e.g. היה אוכל; היה לי כלב).

Impersonal modals are VERB, not AUX (אפשר, אסור), while modals which can be impersonal are only VERB in that construction. Strong modals (with nominal object) are VERB (note they can take accusative objects marked by את):

-	AUX: אני צריךללכת
-	VERB: צריךללכת
-	VERB: אני צריך את המטריה

#### DET or SCONJ?

The definite article DET is used to modify nominals, including nouns and adjectives, while SCONJ is used to modify relative clause heads, typically VERBs and most often present tense forms. A test for SCONJ status is substitutability with ש as a relative marker. Compare:

tree

tree

In the first example, it would be very odd to replace ה with ש, (cf. הגרעין שמייסד?), but in the second example it is possible to retain the same sense with ש (cf. הודעת תנחומים שממוענת…).

#### NOUN or NUM?

For nominalized ordinal numbers, we use the NOUN category, based on the the logic that ordinals are generally ADJ and not NUM, and so the usual treatment of nominalized ADJ applies:

-	מקבעים את אבני השפה אחת/NUM בצמוד לשנייה/NOUN

In this example, אחת is a cardinal number, and therefore tagged NUM. Howeverשנייה is an ordinal functioning as a NOUN (the equivalent for ‘one’ would be ראשונה ‘the first’, which in this context would also be tagged NOUN, and generally as ADJ).

#### NOUN or VERB?

One of the trickier places to distinguish NOUN and VERB is in participles (בינוני) where predicative cases should be tagged VERB, while argument structure participants should be tagged NOUN. The easiest subtypes to resolve are:

-	If a participle carries a definite article or similar determiner, it is a NOUN (but note that the relative marker ה where it is interchangeable with ש does not constitute a determiner for this purpose)
-	If a participle has a subject dependent, it is likely a VERB. An exception to this rule occurs when the predication is actually a form of equative nominal clause, in which case a copula can typically be inserted (for example in יוסי מדריך “Yossi is a guide”, the participle is a NOUN, because we can insert יוסי הואמדריך)

#### PUNCT or SYM?

The main criterion for identifying SYM is pronunciation - PUNCT is generally not read out as a word. Some things that are SYM: $, ₪, / (pronounced “קו נטוי” or “או”), “-” in “3-4” and emoticons/emoji.

The following are PUNCT and not SYM: “-” in “בית - דין” or in “- היא אמרה- ”, the symbol “*” separating paragraphs in a story or unpronounced separators inside a phone number.

#### X or NOUN/PROPN?

The category X is not used for syntactically integrated loanwords, even if they are spelled in foreign characters:

-	אין לי מניות ב-SpaceX/PROPN

## Morphological Features
In the following section, we outline the morphological features for each POS tag, as they pertain to Hebrew.

### Concentrated Table of features

<table>
    <tr>
        <td>POS Tag</td>
        <td>Feature</td>
        <td>Values</td>
        <td>Selected Examples (Invented and from HTB)</td>
    </tr>
    <tr>
        <td>NOUN</td>
        <td>Gender</td>
        <td>Fem</td>
        <td>מנהלת</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Masc</td>
        <td>אצן</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Fem,Masc</td>
        <td>עובדים/ות</td>
    </tr>
    <tr>
        <td></td>
        <td>Number</td>
        <td>Sing</td>
        <td>עבודה</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Plur</td>
        <td>כדורים, עובדות, מעצבים/ות</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>(Dual)</td>
        <td>(שבועיים, שנתיים, יומיים)</td>
    </tr>
    <tr>
        <td></td>
        <td>Definite</td>
        <td>Cons</td>
        <td>Construct State Forms: ילדי הגן</td>
    </tr>
    <tr>
        <td></td>
        <td>Abbr</td>
        <td>Yes</td>
        <td>יו"ר, מנכ"ל, ח"כ</td>
    </tr>
    <tr>
        <td>PROPN</td>
        <td>Abbr</td>
        <td>Yes</td>
        <td>שב"כ, ימ"מ, צה"ל</td>
    </tr>
    <tr>
        <td>ADJ</td>
        <td>Gender</td>
        <td>Fem</td>
        <td>בכירה</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Masc</td>
        <td>מהיר</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Fem,Masc</td>
        <td>גדול/ה</td>
    </tr>
    <tr>
        <td></td>
        <td>Number</td>
        <td>Sing</td>
        <td>קרוב</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Plur</td>
        <td>רחוקים</td>
    </tr>
    <tr>
        <td></td>
        <td>Definite</td>
        <td>Cons</td>
        <td>יפי נפש יפה = Lemma</td>
    </tr>
    <tr>
        <td></td>
        <td>Abbr</td>
        <td>Yes</td>
        <td>תנ"כי</td>
    </tr>
    <tr>
        <td></td>
        <td>Prefix</td>
        <td>Yes</td>
        <td>Modifying a NOUN: אנטי-קומוניסט, מולטי-מיליונר</td>
    </tr>
    <tr>
        <td></td>
        <td>NumType</td>
        <td>Ord  </td>
        <td>Ordinal numbers (ראשון, שני, שלישי, etc.) are tagged ADJ (default Lemma = Masc): זה היה שערו החמישי העונה בליגה. Also for Arabic numerals as ADJ: שחר פאר סיימה במקום ה-3 בעולם.</td>
    </tr>
    <tr>
        <td>ADP</td>
        <td>Case</td>
        <td>Gen</td>
        <td>Only for Genitive preposition: של</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Acc</td>
        <td>Only for Accusative preposition: את</td>
    </tr>
    <tr>
        <td></td>
        <td>Definite</td>
        <td>Def</td>
        <td>For cases of hidden definiteness: יצאתי לטייל בחצר</td>
    </tr>
    <tr>
        <td></td>
        <td>PronType</td>
        <td>Art</td>
        <td>For cases of hidden definiteness: יצאתי לטייל בחצר</td>
    </tr>
    <tr>
        <td>DET</td>
        <td>PronType</td>
        <td>Art</td>
        <td>Only for the definite article ה</td>
    </tr>
    <tr>
        <td></td>
        <td>Definite</td>
        <td>Def</td>
        <td>Def is only for the definite article ה</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Cons</td>
        <td>Cons is relevant for רוב, מעט, שאר, יתר, כול</td>
    </tr>
    <tr>
        <td>PRON</td>
        <td>PronType</td>
        <td>Prs  </td>
        <td>Personal Pronouns = היא, הם, אתן (Lemma: הוא) היא הודיעה כי הוועדה תגבש הצעת חוק בנושא העובדים הזרים.   Reflexive Pronouns (with ADP and in oblique position) = עצמה, עצמן, עצמכם (Lemma: עצמו) קשה להבין כיצד מנהיגים אלה לא שאלו את עצמם שאלה יסודית אחת.   As determiners, meaning the same one, morphologically similar to accusative pronouns, but unsegmented; Lemma:אותו ): היא מבוססת על אותו קרב המגננה שבו אירעו מעשי גבורה עילאיים, ועל קבלת החלטות קשות בתנאי קרב.  Other forms used as determiners meaning the whole/entire one (Lemma: כולו): למכבי תל אביב אין גם כיום שחקן שתמיד יצר את הפער בינו לליגה כולה.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Dem</td>
        <td>Demonstrative Pronouns = זו, אלה (Lemma: זה) טיעונים כאלה, שפעם היו מקובלים בין ליברלים בארה"ב, לא שמעתי כבר הרבה זמן.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Int  </td>
        <td>Interrogative Pronouns = מי, מה   Main clause: במקום שבו שרים, חברי כנסת ואנשי ציבור משמיעים את אמרותיהם, מי זקוק לסאטירה.   Subordinate clause: מקסוול דרש לדווח לו מי הדליף את הנתונים הכספיים של ההוצאה לתקשורת.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Ind  </td>
        <td>Indefinite Pronouns = כלשהו, איזושהי   במאה הזו נבחרו שני סנאטורים לנשיאים, ושלושה נשיאים אחרים כיהנו בסנאט בזמן כלשהו של הקריירה שלהם .   אין ספק כי יש כאן איזושהי אמירה מטאפיסית של היקום על מות האמת.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Emp  </td>
        <td>Emphatic Pronouns (No ADP and often in determiner position) = עצמה, עצמן, עצמכם (Lemma: עצמו) גבולות מדיניים מפרידים בין ארצות, וגבולות אתניים מפרידים בתוך הארצות עצמן.</td>
    </tr>
    <tr>
        <td></td>
        <td>Case</td>
        <td>Gen</td>
        <td>Gen for possessive clitic pronouns: ילדיה = הילדים שלה</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Acc</td>
        <td>Acc for accusative clitic pronouns: להכשילם = להכשיל אותם</td>
    </tr>
    <tr>
        <td></td>
        <td>Definite</td>
        <td>Def</td>
        <td>Mainly for examples like ילדיה. Since we do not assign this feature to the nominal, we must mark somehow that the entire phrase is definite.   Accusative pronouns used as determiners meaning the same one: מדוע לפחות אין הם מפרסמים התנצלות והסברים, באותו תקציב שבו הם משתמשים כדי ליצור תדמית ציבורית חיובית?   Other forms used as determiners meaning the whole/entire one: אחרי עיתונאים יהיו אלה הפוליטיקאים , השוטרים והשופטים , והדבר יביא לקריסת המרקם החברתי כולו.</td>
    </tr>
    <tr>
        <td></td>
        <td>Gender</td>
        <td>Fem</td>
        <td>יתר-על-כן, הריבית בישראל במיוחד על משכנתאות היא מהנמוכות ביותר בעולם, אם לא הנמוכה שבהן.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Masc</td>
        <td>זהו כישלונו היחיד עד כה של המפכ"ל , שנגרם רק בגלל העובדה שהוא סמך יותר מדי על קציניו הבכירים.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Fem,Masc</td>
        <td>The delimiter may vary or be missing altogether: אתם/ן מוזמניםות לפנות אליי בכל נושא. [Forms without explicit reference should not be assigned Gender, e.g., אליי here.]</td>
    </tr>
    <tr>
        <td></td>
        <td>Number</td>
        <td>Sing</td>
        <td>רק אחר-כך נמצא כרטיס ביקור, ובו שמו ותוארו של גרוסבורד כמנהל מפעל פרטי.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Plur</td>
        <td>על פי כל הדיווחים חלה בעשורים האחרונים ירידה תלולה בתמיכה בעבודות מחקר במדעי החברה שאין להן יישום מעשי מידי.</td>
    </tr>
    <tr>
        <td></td>
        <td>Poss</td>
        <td>Yes</td>
        <td>For possessive clitic pronouns: כלכלתה היתה שקועה במיתון קשה, בשעה שרוב המדינות האחרות בארה"ב נהנו משגשוג חסר תקדים.</td>
    </tr>
    <tr>
        <td></td>
        <td>Person</td>
        <td>1</td>
        <td>לשם איזון הנושא, עליי לציין כי אני נמנה על ההורים שהביעו את הסכמתם לקיום הטיול.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>2</td>
        <td>אם בכל זאת אתם רוצים סבסוד למחשבותיכם ולמעשיכם, עליכם להשיג אותו בדרך המיושנת.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>3</td>
        <td>מתקבל הרושם שהיחידים במדינה הזו שאינם צריכים לחשוש מתוצאות מעשיהם הם הפוליטיקאים.</td>
    </tr>
    <tr>
        <td></td>
        <td>Reflex</td>
        <td>Yes</td>
        <td>Coreference with the subject: היא עשתה זאת בעצמה   Coreference with the object: הוא אמר לה לעשות זאת בעצמה   The emphatic pronouns also receive this value: הקרב עצמו נמשך שעות   No explicit reference (predicate-embedded): תראו בעצמכם.</td>
    </tr>
    <tr>
        <td></td>
        <td>Polarity</td>
        <td>Pos</td>
        <td>Pronominal Copulas  (No PronType=Prs): כהונת מושל במדינה גדולה היא קרש קפיצה אל מרכז תשומת הלב הלאומית.</td>
    </tr>
    <tr>
        <td>ADV</td>
        <td>Prefix</td>
        <td>Yes</td>
        <td>Modifying an ADJ: דו-קוטבי, תלת-ספרתי, רב-לאומית ADV: חד-חד-ערכי  VERB: אי אפשר</td>
    </tr>
    <tr>
        <td></td>
        <td>Polarity</td>
        <td>Pos</td>
        <td>Positive: כן, אני מסכים</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Neg  </td>
        <td>Negative Adverbs: בלתי חוקי הוא לא התכוון לכך</td>
    </tr>
    <tr>
        <td>NUM</td>
        <td>Gender</td>
        <td>Fem</td>
        <td>צעד זה של קופת חולים הכללית נעשה במסגרת המאמץ להחזיר אליה חברים שעזבו אותה בחמש השנים האחרונות.   Ambiguous forms – acc. to the context: שיעור האבטלה מתקרב לשמונה אחוזים.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Masc  </td>
        <td>איני יכולה שלא להתריס בפניו על משפט אחד בקטע הפתיחה של הכתבה.   Ambiguous forms – acc. to the context: כל רצוני היה שיאפשרו לי לאחר התמסרות של שמונה שנים לנתח גם במרכז חורב.</td>
    </tr>
    <tr>
        <td></td>
        <td>Number</td>
        <td>Sing</td>
        <td>Numerals are considered singular only when they have a contrast with a plural form. They should not receive this feature if no such contrast exists: זו אחת הסיבות לכך שמענקי מקארתור מבוקשים כל כך. (cp. אחדות)</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Plur</td>
        <td>Clear contrast (אלף-אלפים,מאה-מאות, עשר/ה-עשרות, מיליון-מיליונים)), Cp. Singular, מדיווח שהגיע אתמול למשרד התיירות בירושלים, ישתתפו במשלחת כמאה מראשי הקהילה . vs. Plural, משום כך, ראשית כל עליך לחשוב על קבלת תפקיד חשוב באחד ממאות הגופים הללו.</td>
    </tr>
    <tr>
        <td></td>
        <td>Definite</td>
        <td>Cons</td>
        <td>Some numerals have overt CS forms שני הילדים (שתיים = Lemma) אלפי המפגינים (אלף = Lemma) Cp. שלושת הכדורים with a distinct CS form (Lemma = שלוש) vs. שלוש המנהלות which has the same form as the bare numeral, but different pronunciation (shalosh vs. shlosh)</td>
    </tr>
    <tr>
        <td></td>
        <td>NumType</td>
        <td>Card</td>
        <td>Cardinal numbers (אחת, שתיים, שלוש, etc.) are tagged NUM (default Lemma = Fem): נשיא אירלנד נבחר לשבע שנים. But not for Arabic numerals as NUM: לפני שהפסידה במשחק הרביעי, היא ניצחה ב-3 משחקים ברציפות.</td>
    </tr>
    <tr>
        <td>AUX      </td>
        <td>VerbType</td>
        <td>Cop</td>
        <td>Forms of היה: אתה מתבונן בנושא שיהיה בעל ערך לגיטימי למחקר, ובו בזמן יכבוש את דמיונו של פקיד התוכנית.   The inflected forms of הִנֵּה (הינו, הינה, הינם, הינן): מרכיב חשוב ביותר בתהליך קבלת החלטות הינו המידע. Inflected forms of אין only as pure copulas: תחנת הקלפי אינה מקום ההצבעה ההכרחי היחיד.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Mod</td>
        <td>האם אתם יכולים להתעלות על כך?   שום קרן או חברה מסחרית לא תהיה מוכנה לתת לו מימון אם ייוודעו פרטיו.</td>
    </tr>
    <tr>
        <td></td>
        <td>Polarity</td>
        <td>Pos</td>
        <td>Forms of היה: המשבר במפרץ הפרסי לא היה נושא בחירות מרכזי באיובה.   The inflected forms of הִנֵּה (הינו, הינה, הינם, הינן): דיווח אמין הינו ערך יסודי חיוני ומקודש במשרד המשטרה.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Neg</td>
        <td>Assertion auxiliaries: האשמה אינה בשיטה או במדגם.</td>
    </tr>
    <tr>
        <td></td>
        <td>Gender</td>
        <td>Fem</td>
        <td>מורשת הקרב שצה"ל אימץ כתוצאה מהקרב על מנזר סן סימון איננה מיתוס.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Masc</td>
        <td>אם אתה מעדיף לפעול בלי איש ביניים, אתה יכול להקים ארגון משלך שלא למטרות רווח.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Fem,Masc</td>
        <td>את.ה יכול/ה להקים ארגון משלך שלא למטרות רווח.</td>
    </tr>
    <tr>
        <td></td>
        <td>Number</td>
        <td>Sing</td>
        <td>תחנת הקלפי שוב אינה מקום ההצבעה ההכרחי היחיד.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Plur</td>
        <td>הדמוקרטים היו באווירת אופוריה, והניחו שמועמדם דוקאקיס לא יצטרך אפילו לרוץ לבית הלבן .</td>
    </tr>
    <tr>
        <td></td>
        <td>Person</td>
        <td>1</td>
        <td>מסתבר שהייתי תמים.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>2</td>
        <td>עם הרבה עץ, מכונת אספרסו עתיקה כמעט ושטח גן מקסים, הוא מסוג המקומות הפשוטים והמזמינים שהייתם מצפים למצוא בין גבעות טוסקאנה.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>3</td>
        <td>זו הייתה התרברבות לא-דיסקרטית, ואין ספק שיימנע ממנה השנה.</td>
    </tr>
    <tr>
        <td></td>
        <td>Tense</td>
        <td>Past</td>
        <td>הביקור הקצר היה רווי מתח ודרמטי .</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Present</td>
        <td>Inflected forms of אין only as pure copulas: תחנת הקלפי אינה מקום ההצבעה ההכרחי היחיד.  The inflected forms of הִנֵּה (הינו, הינה, הינם, הינן): תחנת הקלפי הינה מקום ההצבעה ההכרחי היחיד</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Fut</td>
        <td>מצב קופת ההתאחדות לספורט בהחלט יהיה פחות טוב, אם למרות הכל יממש ללקין את נסיעתו לאוסטרליה.</td>
    </tr>
    <tr>
        <td>VERB</td>
        <td>Gender</td>
        <td>Fem</td>
        <td>תופעה זו התבררה אתמול בוועדת העבודה והרווחה של הכנסת, שדנה בנושא העסקת עובדים זרים.  The inflected forms of יש (ישנו, ישנה, ישנם, ישנן):  בארצות הברים ישנן חבילות של מבחנים ומבדקים שנקבעו מראש.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Masc</td>
        <td>החלטתי לקחת יוזמה ולטפל בבעיה בעצמי. We should not assign this feature when it is impossible to figure out the gender from the context.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Fem,Masc</td>
        <td>Overt mixed reference: א.נשים בחברה דיווחו שהםן מרוצים/ות.</td>
    </tr>
    <tr>
        <td></td>
        <td>Person</td>
        <td>1</td>
        <td>כהיסטוריון, הוא בוודאי רוצה שנתעד, נלמד ונשתדל להבין את הרקע שהוליד את התופעה הנאצית.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>2</td>
        <td>אבל אל תטרחו לדווח לשלטונות המקומיים.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>3  </td>
        <td>איש לא התכוון לכך .</td>
    </tr>
    <tr>
        <td></td>
        <td>Number</td>
        <td>Sing</td>
        <td>בחודש הבא, הוא יבקר את בני משפחתו בצרפת.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Plur  </td>
        <td>עמדנו בתור במשך שעות.  The inflected forms of יש (ישנו, ישנה, ישנם, ישנן):  ישנם ויכוחים רבים על דרכי הטיפול ויעילותן</td>
    </tr>
    <tr>
        <td></td>
        <td>Tense</td>
        <td>Pres</td>
        <td>Present (same form as Participles): הם מקבעים את אבני השפה אחת בצמוד לשנייה.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Past</td>
        <td>Past: בתקופה זו שימשה כמנהלת המחלקה לביטחון מידע.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Fut</td>
        <td>Future: בסוף השנה יעלה לאקרנים סרטו התשיעי הבמאי קוונטין טרנטינו.</td>
    </tr>
    <tr>
        <td></td>
        <td>Voice</td>
        <td>Act</td>
        <td>Active voice refers to an action that the subject is performing or a state that the subject is in: היא למדה באוניברסיטת בוסטון.  בקצהו עומד מנזר "יוחנן במדבר".  יוסי בישל פסטה.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Mid</td>
        <td>Middle voice refers to reciprocal or reflexive actions, and often when no external agent is understood or implied: אבל השיטים נזכרת בספר במדבר (*על-ידי כותבי המקרא). הפסטה התבשלה (*על-ידי יוסי).</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Pass</td>
        <td>Passive voice refers to an action that is performed on the subject, e.g., נגד רביב הוגש בסופו של דבר כתב אישום (על-ידי הפרקליטות). הפסטה בושלה ע"י יוסי.</td>
    </tr>
    <tr>
        <td></td>
        <td>Mood</td>
        <td>Imp</td>
        <td>Reserved for Imperatives, which in Hebrew appear only in the 2nd person (sing or plur), e.g. קח אותו החוצה. צאו לטייל ברחבי הארץ. תן לי את הלחם.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Irr</td>
        <td>Reserved for beinoni, in contra-factual constructions. No Tense feature should be assigned to the beinoni in this case, e.g., היינו רגועים יותר, לו הצבא האמריקאי היה מעדכן את צה"ל בכל הנוגע לתכונותיו של הצבא העיראקי , בכוויית ובעיראק עצמה.</td>
    </tr>
    <tr>
        <td></td>
        <td>Aspect</td>
        <td>Prog</td>
        <td>Reserved for beinoni, in past progressive meaning. No Tense feature should be assigned to the beinoni in this case, e.g.,  אחת לכמה שבועות הייתי נקלע לאסיפת המונים שכינס במרכז ירושלים.</td>
    </tr>
    <tr>
        <td></td>
        <td>VerbForm</td>
        <td>Part  </td>
        <td>Present Participles (beinoni): היא כיתבה את השרה הממונה על ניהול משבר הקורונה.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Inf  </td>
        <td>Infinitive forms (In all verbal templates but passives PUAL and HUFAL): בני משפחת מנגיסטו דרשו מהצלב האדום לסייע להחזיר את בנם ארצה. The first infinitive לסייע is Active in HebBinyan = PIEL The second infinitive להחזיר is also Active in HebBinyan = HIFIL    הוא השאיר את הפסטה על הכיריים להתבשל  The infinitive להתבשל is Middle in HebBinyan = HITPAEL</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Vnoun</td>
        <td>For infinitive absolute in clear adverbial clause uses. Cp. עד רדת הלילה which is a simple ADP+NOUN  vs. ברדתו אל הבאר, מעד ונחבל בראשו Which is segmented ב|רדת|ו with ב as SCONJ+mark With רדת as VERB+advcl With ו as PRON+nsubj</td>
    </tr>
    <tr>
        <td></td>
        <td>VerbType</td>
        <td>Mod</td>
        <td>חלק גדול מהסצנות מרגישות לא מלוטשות, הניראות הכללית של המשחק מרגישה מיושנת, ולא בטוח שגם שדרוג החומרה שלכם יצליח לגרום לעובדה הזאת להשתנות. [VERB+csubj = בטוח←יצליח]</td>
    </tr>
    <tr>
        <td></td>
        <td>Polarity</td>
        <td>Pos</td>
        <td>As per the HTB convention, the various forms of to be (e.g., היה, יהיו) in existential meaning are tagged VERB, and receive this value: היו אנשים בעלי אמצעים שתרמו דרך קבע לפעילות של התנועה ולפעילות של הישיבה.   Forms of יש (existential): הם פשוט תהו, נזכר לנקובסקי, "למי יש רעיונות מעניינים?"</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>Neg  </td>
        <td>Negative existential forms: אין ביניהם אנשי ציבור הרצים לכנסת בשם פתרונותיהם.</td>
    </tr>
    <tr>
        <td></td>
        <td>HebBinyan (Universal Features)</td>
        <td>PAAL</td>
        <td>היא אמרה כי שירות התעסוקה הציע להביא עובדים מדרום לבנון, אך תנועת המושבים סירבה.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>PIEL</td>
        <td>היא הודיעה כי הוועדה תגבש הצעת חוק בנושא העובדים הזרים.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>HIFIL</td>
        <td>רייזמן מציע שהממשלה תסבסד מחצית מעלות העסקתם של העולים בקטיף .</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>HUFAL</td>
        <td>הרבה כסף הוקדש להגות על מדינות העולם השלישי .</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>PUAL</td>
        <td>תשדירי הבחירות שלו תוארו כשנונים ביותר בארה"ב .</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>NIFAL</td>
        <td>אנו, נפגעי המשכנתאות, נקלענו למצבנו הקשה לא בגלל ריבית נמוכה, אלא בגלל האינפלציה וההצמדה.</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td>HITPAEL</td>
        <td>כך או כך, שמו של בורחס יצטרף מעתה לרשימה הארוכה של הסופרים שהפרס נמנע מהם.</td>
    </tr>
</table>

### Nominal Features
#### Gender
Hebrew inflects nominals (NOUN, PRON), predicates (VERB, AUX) and modifiers (ADJ, NUM) for Gender. We distinguish between the values Fem, Masc and Fem,Masc standing for feminine, masculine and overt mixed reference respectively. 

Morphologically Masculine nominals and verbs, referring to a mixed group (males and females) receive the value Masc. It is based on conventions from other TBs where this occurs:

המסירות הבלתי-נלאית של מיקרוסופט והחיבור המוצלח עם גוגל יבטיחו את המשך שיתוף הפעולה ביניהן.

If the gender cannot be determined, it might be better to assign Fem,Masc. In cases where Gender cannot be determined from the context, we will not assign this feature. In cases where it is clearly referring to a specific Gender (Fem or Masc), then we should annotate accordingly.     

Some examples:
האצן המהיר.
both the NOUN אצן and the ADJ מהיר receive the feature Gender=Masc;
המנהלת הבכירה. 
both the NOUN מנהלת and the ADJ בכירה receive the feature Gender=Fem;
דרושים/ות עובדים/ות למשרה נחשקת. 
both the VERB דרושים/ות and the NOUN עובדים/ות receive the feature Gender=Fem,Masc.

#### Number
Hebrew inflects nominals (NOUN, PRON), predicates (VERB, AUX) and modifiers (ADJ, NUM) for Number. We distinguish between the values Sing and Plur standing for singular and plural respectively. 
Some examples:
הגשם הראשון.
Both the NOUN גשם and the ADJ ראשון receive the features Number=Sing and Gender=Masc. (The ADJ ראשון should also receive NumType=Ord, see NumType).
עבודה קשה.
Both the NOUN עבודה and the ADJ קשה receive the features Number=Sing and Gender=Fem;
כרישים נצפו קרוב לחופי הים התיכון. 
Though the NOUN כרישים refers to a group of sharks comprising of males and females, and the VERB נצפו is morphologically shared for both Genders, we follow other TBs conventions in assigning both Number=Plur and Gender=Masc.

### Lexical Features
#### Definite
Definiteness is expressed in Hebrew using a definite article in the form of the morpheme ה attached to nominals (not to be confused with the relative clause marker ה preceding participles (beinoni) and standing for ש/אשר). We assign the definite article, tagged DET, the values Definite=Def and PronType=Art. Adjectives modifying a definite Noun also require a repetition of the definite article. Therefore, in the phrase הילדה החכמה, both articles receive these features.

In cases of hidden definiteness, as in prepositions ב, ל, e.g., הלכתי לבית, נסענו באוטובוס, we do not introduce pseudo-tokens by segmenting the phrase to ל_ה_בית, but rather indicate the definiteness on the preposition itself by assigning both Definite=Def and PronType=Art.

Construct State (CS henceforth) forms are special forms of nominals (and some modifiers) when appearing in compound (smixut) constructions. In the phrase משרתי המלכה, the first nominal משרתים appears in its CS form. A definite article is implicit before משרתי when the second nominal is definite, due to definiteness spreading from the second nominal, מלכה. 
As stated above, we do not introduce pseudo-tokens by segmenting the nominal משרתי as המשרתים_של_. Instead, we indicate this special form by assigning it the feature Definite=Cons. The article attached to the second nominal (המלכה) receives the features Definite=Def and PronType=Art. 

Please note the difference between these two values: while not strictly definite per se, Definite=Cons allows us to differentiate the CS form (e.g., ליצנֵי) from the bare noun (e.g., ליצנים). Definite=Def is the value we assign the definite article attached to the bare noun (e.g., הליצנים) to render some non-specific jesters into specific, definite ones. The phrase ליצני חצר, then, is not definite, but rather equivalent semantically to some generic court jesters, ליצנים של חצר. Nevertheless, we need to indicate that the surface form of ליצנים is different, i.e., the CS one. UD or HTB decided to assign the value Cons under the Definite feature.
The phrase ליצני החצר, however, is definite, by virtue of definiteness spreading from the second nominal (החצר). Therefore, it is semantically equivalent to some specific court jesters, הליצנים של החצר. The value Definite=Cons assigned to ליצני in both cases to simply indicate the morphological CS form, does not make the entire phrase definite.

#### Poss
When we have a nominal with a clitic possessive pronoun, as in תלמידיה,  it is essentially equivalent to the phrase התלמידים שלה. Since we do not introduce pseudo-tokens, as stated above, and we do not mark definiteness on the nominal itself, we indicate definiteness by marking Definite=Def and Poss=Yes on the clitic pronoun (in addition to other features, to be stated below). Note that we do not indicate PronType=Art in this case, since it is actually a personal pronoun and requires the feature PronType=Prs.

At the moment, indicating possession with the deprel nmod:poss and the morphological feature Poss=Yes seem redundant. However, this is the convention in other TBs. We also have the double genitive construction in Hebrew, תלמידיו (היקרים) של המורה, which, though superfluous,  requires two nmod:poss relations, one within the phrase תלמידיו and another between תלמידי and מורה, but only the former receives the Poss=Yes feature.

We assign this feature to the pronoun in segmented possessive pronouns:
התלמידים שלה ← ה|תלמידים של|ה 

#### PronType
There are several pronominal types which are relevant to Hebrew:
-	The definite article ה and prepositions with hidden definiteness (ב, ל) receive PronType=Art (see Definite).

-	The personal pronouns (PronType=Prs) inflect based on Gender, Number and Person. 

The 1st person pronouns אני and אנחנו (as well as the less common forms אנוכי and אנו) refer to singular and plural speakers, respectively. Their Gender may be left unassigned in the absence of clear reference.

The 2nd person pronouns (את, אתה, את/ה | אתן, אתם, אתם/ן) refer to singular and plural addressees with Gender Fem, Masc and Fem,Masc respectively. 

The 3rd person pronouns (היא, הוא, הוא/היא | הן, הם, הם/ן) refer to singular and plural third parties with Gender Fem, Masc and Fem,Masc respectively. Note that the pronominal copulas have the same form but they do not receive PronType=Prs. Rather, they receive Polarity=Pos (see Polarity).

Their default Lemma is הוא.

As Omer & Shira suggested, I recommend segmenting the forms שנינו, שניכם, שניהם | שתינו, שתיכן, שתיהן, instead of referring to them as frozen forms to address two entities. Since this is possible, in theory, with other numerals (שלושתכן, ארבעתכם, etc.), we might want to treat all these forms as a NUM+PRON with deprel nummod from the PRON to the NUM. The NUM in these cases have the Definite=Cons feature solely based on its morphologically CS form (שני+הם, שלושת+ן, ארבעת+כן, etc.)

-	Some pronouns can be used as determiners in the sense of “the same one”. They are morphologically identical to the accusative pronouns (אותו, אותה, אותם, אותן), but unlike the latter, we leave them unsegmented. They are tagged PRON with deprel det, e.g.,
הוא ימשיך להתגלגל, לאו דווקא באותן השיטות. 
Here we assign the following features to אותן (default Lemma is אותו):
Definite=Def | Gender=Fem | Number=Plur | Person=3 | PronType=Prs

-	As Omer suggested, we might want to add to this list the forms of כולו (Lemma=כולו; Inflection: כולו, כולה, כולם, כולן) when they function as determiners in the sense of “the whole/entire one”. Just as above, they should not be segmented. I suggest tagging them PRON with deprel det, e.g.,
למכבי תל אביב אין גם כיום שחקן שתמיד יצר את הפער בינו לליגה כולה.
Here we assign the following features to כולה (default Lemma is כולו):
Definite=Def | Gender=Fem | Number=Sing | Person=3 | PronType=Prs

Alternatively, we may want to introduce PronType=Tot for Total/Collective pronouns, adjectives and determiners, since they have full inflection for Number and Gender.

That said, we should distinguish the above forms from the nominalized usage of כולם which is unsegmented and tagged NOUN.

-	The demonstrative pronouns (PronType=Dem) refer deictically to a third party entity:
זה, זהו, זו, זוהי, זאת | אלה, אלו. 

Note that זאתי is not considered grammatical. If it appears in the data, add Typo=Yes and CorrectForm=זאת (see under Additional Features). The plural forms are interchangeable and refer to either Gender.

The default Lemma is זה (Person=3).

-	The reflexive pronouns (PronType=Prs and Reflex=Yes) refer anaphorically to an entity in the subject position (whether explicit or not, as in תראו בעצמכם) or object/oblique position and they are preceded by a preposition. They inflect for Gender, Number and Person:
The 1st person reflexive pronouns, e.g.,
 ראיתי את עצמי במראה.
 הצענו את עצמנו כמתנדבים אפשריים.
 היא אמרה לי לעשות זאת בעצמי.

The 2nd person reflexive pronouns, e.g.,
 אתה יכול למצוא את הפתרון בעצמך.
 אתן חייבות להקדיש זמן לעצמכן.

The 3rd person reflexive pronouns, e.g.,
 אילו היה עושה זאת, הוא היה עלול להביך את עצמו.
 אך לפני הכל ארה"ב חייבת להבהיר לעצמה לשם מה הקימה את העולם על רגליו.
 אבל הן גם יצטרכו עכשיו לממן בעצמן את ההוצאות שלהן.

The default Lemma is עצמו.

-	The emphatic pronouns (PronType=Emp and Reflex=Yes) appear in the bare forms of עצמו and they follow an entity in subject or an object/oblique position to emphatically refer back to them: 
הקרב עצמו נמשך שעות. 
הגשתי למרצה את הטיוטה עצמה בצירוף מספר נספחים. 

We refer to them as emphatic pronouns, because they can be left out while keeping the sentence grammatical, unlike the reflexive pronouns above.

The default Lemma is עצמו.

-	The indefinite pronouns (PronType=Ind) refer to an indefinite third party: מישהו, כלשהו, איזשהו, מישהי, איזושהי, כלשהי, כלשהם, כלשהן. While they can be thought of as segmented to מי_ש_הוא, as mentioned above, we will not introduce pseudo-tokens, and instead leave them unsegmented.
מישהו יכול לפנות לפתרון בעיה כלשהי ולהיתקל באיזשהו מכשול בדרך.

The default Lemma for each will be its Masc.Sing.3 variant, i.e., מישהו, כלשהו, איזשהו in the above example.
  
-	The interrogative pronouns (PronType=Int) are those used to introduce either a main question or a subordinate one: מי, מה, למה, איך, איזו, איזה, אילו

Main question: 
מה פשר המושג הזה?
באיזו מידה יכול אדם להשתנות?
Subordinate question:
אני לא יודע מה יקרה.
לימדת אותי איך לחשוב בצורה ביקורתית.
While the POS tag of the above examples is not always PRON (e.g., איך is tagged ADV), they all carry the feature PronType=Int.

#### Case
Case is generally a nominal feature. Languages rich in morphology have several conjugations for each noun based on case (Nominative, Accusative, Genitive, Dative, etc.). However, Hebrew nouns are invariant and we indicate case using certain prepositions.

#### Case
Case is generally a nominal feature. Languages rich in morphology have several conjugations for each noun based on case (Nominative, Accusative, Genitive, Dative, etc.). However, Hebrew nouns are invariant and we indicate case using certain prepositions.

Genitive case indicates some form of relation, ownership or possession. When the preposition של precedes a nominal, it carries the feature Case=Gen.
התלמידים היקרים של המורה  

Note that we segment the possessive pronouns to their components:
התלמידים שלה ← ה|תלמידים של|ה

The preposition receives Case=Gen and the clitic pronoun receives the features 
Gender=Fem | Number=Sing | Person = 3 | Poss=Yes | PronType=Prs 

In the equivalent form תלמידיה, we assign the clitic possessive pronoun the feature Case=Gen, in addition to those mentioned before (see Definite & Poss):
Case=Gen | Definite=Def | Gender=Fem | Number=Sing | Person=3 | Poss=Yes | PronType=Prs

Accusative case indicates the direct object, often the patient participant in transitive constructions. In Hebrew the feature Case=Acc is marked on the preposition את before a definite nominal:
חברי התנועה המארגנים יבקשו לנצל את ההיתר הזה

Or before compound (smixut) phrases which are considered definite:
זו לא הריבית שסיבכה את מקבלי המשכנתאות

Also before nominals with clitic possessive pronouns:
אני נמנה על ההורים שהביעו את הסכמתם לקיום הטיול

In higher registers, verbs can have clitic accusative pronouns:
אבקשכן לשלוח אליי חומר זה ← אבקש את|כן לשלוח אליי חומר זה

Here the features of the clitic pronoun are:
Case=Acc | Gender=Fem | Number=Plur | Person=2 | PronType=Prs

#### Reflex
Reflexive and emphatic pronouns receive the feature Reflex=Yes, the former is of type PronType=Prs, the latter of type PronType=Emp (see above under PronType). 

#### NumType
Hebrew distinguishes between two types of numbers: Cardinal (NumType=Card) and Ordinal (NumType=Ord). While Cardinal numbers (אחת, שתיים, שלוש, etc.) are tagged NUM with a default Fem Lemma (e.g., ארבעה has the Lemma ארבע), the Ordinal numbers (ראשון, שני, שלישי, etc.) are tagged ADJ with a default Masc Lemma (e.g, הבעיות הראשונות has the Lemma ראשון).

As mentioned above (see under PronType), some cardinal numerals have overt CS forms (e.g., שני התלמידים, שתי הסטודנטיות, both with Lemma=שתיים). Some forms on the surface do not differ from the bare form (e.g., חתכתי לסלט את שלוש העגבניות vs. אכלתי את שלושת הממתקים שנשארו), but all of these forms receive the feature Definite=Cons solely based on their morphologically CS form.

We recommend assigning the Number feature to Cardinal numbers only when they have clear contrast between singular and plural morphology, e.g., אלף-אלפים, מאה-מאות, עשר/ה-עשרות, מיליון-מיליונים, etc. If no such contrast exists, we should not assign the Number feature, 
e.g., שתי סטודנטיות, שלושה כדורים, ארבע תחנות, etc.

Most Cardinal and Ordinal numbers do inflect for Gender. We should assign the Gender feature with values Masc or Fem based on the form , e.g., ארבעה כדורים receives Gender=Masc and ארבע תחנות receives Gender=Fem. 

We assign morphological features to Arabic numerals only when they are tagged ADJ, but not when they are tagged NUM: 
3 תחנות = No morphological features
3 כדורים  = No morphological features
המשחק ה-3 = Gender=Masc|NumType=Ord
התחנה ה-3 = Gender=Fem|NumType=Ord


Some numerals do not have a Gender contrast, עשרים עגבניות vs. עשרים תפוחים. In such cases, the Gender value will be determined by the modified NOUN. If it appears in isolation with no clear disambiguating context, it should not receive the Gender feature.

Most Ordinal numbers, as regular Adjectives, have clearer forms for Number and Gender (e.g., ראשון, ראשונה, ראשונים, ראשונות).

If the Gender is incorrect, assign the features according to the correct form (e.g., חמש כדורים receives the feature Gender=Masc). In those cases, we add CorrectForm=חמישה and Typo=Yes (see under Additional Features).

### Verbal Features
#### Tense
Hebrew finite verbs have three temporal conjugations, some of them complete for each verbal template: Past, Future and Present. Note that the Present forms are the same as those of participles. We do not assign Tense to infinitives (VerbForm=Inf) or to imperatives (Mood=Imp).

Verbs inflect for Gender, Number and Person. Some forms share Gender (e.g., present verbs for singular are the same for each person, and the same goes for plural; 3rd person plural forms in the past and future are the same for both genders). As mentioned before, if it is possible to determine the gender (Fem or Masc), then we should annotate accordingly. If it refers to a mixed group, we should follow other TBs and annotate Masc. If it is impossible to determine the gender from the context, we should not assign this feature.

Past:
לא שמעתי כבר הרבה זמן טיעונים כאלה.
Future:
הממשלה תסבסד מחצית מעלות העסקתם של העולים בקטיף.
Present & Participles:
הראש היהודי ממציא לנו פטנטים בדמות מתנדבים, המשולמים שכר מחפיר.

The first is a finite verb with the following features:
Gender=Masc | HebBinyan=HIFIL | Number=Sing | Person=3 | Tense=Pres | VerbForm=Part | Voice=Act

The second is a participle (following the SCONJ ה) with the following features:
Gender=Masc | HebBinyan=PUAL | Number=Plur | Person=3 | Tense=Pres | VerbForm=Part | Voice=Pass

#### Voice
Finite verbs, Imperatives, Infinitives and Participles carry the Voice feature. We distinguish in Hebrew between three possible values: Active, indicating that the subject is performing an action or is in a certain state; Passive, denoting that the subject is undergoing an action or is being acted upon; and Middle, which are rather trickier to figure out (see HebBinyan for more examples). A common distinction between Active, Passive and Middle respectively is to consider whether an external agent is mentioned in the sentence (or can be understood):

יוסי בישל פסטה
הפסטה בושלה ע"י יוסי
הפסטה התבשלה


Note that the following is ungrammatical:
*הפסטה התבשלה ע"י יוסי

We can demonstrate the difference with the HebRoot שני with examples from the HTB. The Active voice in transitive constructions requires two participants, one performing the action and one receiving it. Here, קופת חולים הכללית is termed the Agent; התעריף… is termed the Patient, the direct object.
Active →
קופת חולים הכללית שינתה את התעריף לשמירת זכויות החברות בקופה ליוצאים לתקופה ממושכת לחו"ל.
Gender=Fem | HebBinyan=PIEL | Number=Sing | Person=3 | Tense=Past | Voice=Act

The Middle voice generally denotes a reflexive meaning (the subject is the internal object of an action, or the subject is performing an action to their benefit). It can also refer to an action without an explicit, or even implicit, external agent. Here, המצב needs to change, and it is understood as a process that needs to happen without referring to some third party who will undertake this change.
Middle → 
המצב צריך להשתנות בהתאם (*ע"י החברה)
HebBinyan=HITPAEL | VerbForm=Inf | Voice=Mid

The Passive voice indicates that the subject is being acted upon. An Agent, if explicitly mentioned, often appears inside a by-phrase (על ידי/ע”י + nominal)
Passive → 
ב1945 שונה שמה של קניגסברג לקלינינגרד (ע"י ברית המועצות)

Gender=Masc | HebBinyan=PUAL | Number=Sing | Person=3 | Tense=Past | Voice=Pass


Shira recommended consulting this article on the connection between HebBinyan and Voice:
(עידית דורון) תרומתו של הבניין למשמעות הפועל

#### Mood
This feature is reserved in Hebrew for Imperatives, a form of command or strong request to addressees (2nd person singular or plural). We assign the value Mood=Imp to such examples as:
אנא דאג לנעילת הכניסה אל הגג.
קחו למשל את אריק ובנץ.
Note that these forms do not have Tense.

We also assign the value Mood=Irr, short for irrealis, to beinoni forms in contra-factual constructions. Note that the Tense feature should not be assigned to the beinoni in this case.

היינו רגועים יותר, לו הצבא האמריקאי היה מעדכן את צה"ל בכל הנוגע לתכונותיו של הצבא העיראקי, בכוויית ובעיראק עצמה.

#### Aspect
This value Aspect=Prog, short for progressive, is assigned to beinoni forms in the past progressive - structurally, even if we cannot make sure the semantic meaning is indeed progressive. Note that this is predominantly in constructions with derivations of היה. Note that the Tense feature should not be assigned to the beinoni in this case. Instead, the dependent AUX should carry the Tense.

אחת לכמה שבועות הייתי נקלע לאסיפת המונים שכינס במרכז ירושלים.

ניתן היה לעשות זאת

 Could be a simple past form (ניתן+past aux), irrealis (it could have been done), and not necessarily progressive, but we still mark it as such, because of its structure. 

#### Person
Verbs and Pronouns inflect for person: 1st person - speaker; 2nd person - addressee; 3rd person - third party. See PronType and Tense for examples.

#### VerbForm
We distinguish in Hebrew between three possible values: Part standing for Participles, Inf standing for Infinitives, and Vnoun for Infinite Absolute in clear adverbial constructions. 

Note that both Present finite verbs and Present Participles receive the features 
Tense=Pres | VerbForm=Part (see Tense).

Infinitives are non-finite verbal forms. They can stand as clausal subjects or follow finite verbs. 
They do not have Tense or Person, but they do carry Voice. 
Except Passive verbal templates (PUAL and HUFAL), all other templates have a unique infinitive form, e.g.,

PAAL → 
מדובר בקומץ אנשים שניסו למשוך אליהם כספי מענקים.
HIFIL →
ברצוני להדגיש משפט אחד בדבריה.
HITPAEL →
שגב החליט שלא להתייחס לכך בכתבתו.

The value VerbForm=Vnoun is assigned to infinitive absolute forms in adverbial clause constructions. We should distinguish between simple nouns, predominantly in smixut constructions, e.g., עד רדת הלילה, and clear adverbial clauses, 
e.g., ברדתו אל הבאר, מעד ונחבל בראשו.
 
We segment ב|רדת|ו in the following manner: ב is an SCONJ+mark; רדת is VERB+advcl with the features VerbForm=Vnoun|Voice=Act|HebBinyan=PAAL andLemma ירד; and the pronominal suffix ו is PRON+nsubj. 
Copular Vnoun forms, however, like היותו should be left unsegmented, consistent with היה-הייתן...

#### VerbType
Hebrew has two possible values for this feature: Cop standing for Copula, and Mod standing for Modal verbs and auxiliaries.
Only forms of היה receive the value VerbType=Cop (see Polarity):
זו הייתה התפילה הקשה ביותר בחיי.

Here הייתה, tagged AUX (with deprel cop), receive the following features:
Gender=Fem | Number=Sing | Person=3 | Polarity=Pos | Tense=Past | VerbType=Cop

The inflected forms of הִנֵּה (tagged AUX ;הינו, הינה, הינם, הינן), standing for a copula:
הרשת הינה בבעלותו של איש העסקים יהושע סטרויסקי.
Gender=Fem | Number=Sing | Person=3 | Polarity=Pos | VerbType=Cop | Tense=Pres.


Modal verbs and auxiliaries belong to a closed POS class (see list of possible Lemmas under AUX in POS tagging in Detail):
החברה שלנו צריכה להיות פתוחה יותר.

Here the root is the ADJ פתוחה. The nsubj is חברה in החברה שלנו.
We have two auxiliaries (tagged AUX):

The first is modal, צריכה, which receive the following features:
Gender=Fem | Number=Sing | Person=3 | VerbType=Mod

THE second, להיות, is an Infinitive functioning as a copula:
Polarity=Pos | VerbForm=Inf | VerbType=Cop

We attach the root to להיות with deprel cop, and to צריכה with deprel aux.

#### HebBinyan
All Hebrew verbs have unique conjugations based on verbal templates called Binyanim. The seven verbal templates can be (loosely) divided into three groups, based on Voice. We assign this feature under Universal Features (formerly under Misc Features):

Active → PAAL, PIEL, HIFIL
הוא קרא להפסקת התופעה.
הדבר סייע להכניס את הספר לרשימת רבי-המכר של "ניו יורק טיימס".

היא הודיעה כי הוועדה תגבש הצעת חוק בנושא העובדים הזרים.

Middle → NIFAL (often reciprocal, but may also be passive), HITPAEL (often in reflexive meaning, but may also be active)
בייקר נפגש אתמול עם נשיא צרפת.
הוא התגלח והתארגן לפני שיצא לעבודתו. 

Passive → PUAL, HUFAL
כל עובד ישראלי שיסכים לעבוד בענף החקלאות ישובץ לעבודה לאלתר.
עד סוף השנה לא תועסק בארץ אף אחות כעובדת זרה.

### Additional Features
#### Polarity
The feature Polarity has two possible values: Pos for positive and Neg for Negative. We assign 
Polarity=Pos to the following POS with specific tokens, as detailed below.

Pronominal copulas, tagged PRON (הוא, היא, הם, הן):
דיווח אמין הוא ערך יסודי, חיוני ומקודש במשרד המשטרה.
Note that these forms do not receive the feature PronType=Prs.

The inflected forms of הִנֵּה (tagged AUX ;הינו, הינה, הינם, הינן) are often used in place of a copula. While this use is not considered grammatical according to the Hebrew Academy, they are nonetheless prevalent, particularly in journalism and academic papers.
מרכיב חשוב ביותר בתהליך קבלת החלטות הינו המידע.
הרשת הינה בבעלותו של איש העסקים יהושע סטרויסקי.


All the forms of היה in copulative function receive this feature, tagged AUX (היה, היו, יהיה, יהיו):
הקרב היה אחד הקרבות הקשים והמפוארים בכל קרבות ישראל.
שנים אלו יהיו גורליות בכמה מישורים.
Note that the feature VerbType=Cop is assigned only to these forms, but not to the pronominal copulas.


We assign Polarity=Neg to the following POS with specific tokens, as detailed below.

Assertion auxiliaries, tagged AUX (e.g., איני, אינך, אינה, אינכם, אינם):
ההבחנות אינן ברורות.
We suggest adding Tense=Pres to these forms only when they function as pure copulas, but not when they negate a participle or a present tense verb, e.g.,:
"איני תומכת באף אחד מן הצדדים ", הכריזה.

Negation adverbs, tagged ADV (e.g., אי, לא, אין):
אין יודעים אם מדובר בהורה בודד, או בקבוצת הורים מסוימת.

Existential verbs (יש) receive the feature Polarity=Pos. They are contrastive to אין in existential meaning (more precisely the lack thereof) which receives the feature Polarity=Neg. E.g,

אין דרך אחת טובה, ולפעמים אנחנו מתכננים תוכניות והדרך משתנה.
באופן טבעי אנחנו מחפשים יזמים שאנחנו מאמינים שיש להם פוטנציאל לבנות ולהוביל חברות ענק.

#### Prefix
The feature for affixation, predominantly a prefix in Hebrew, is Prefix=Yes. 

When modifying a NOUN, a prefix is tagged ADJ:
אני שומע שאתם הולכים להצביע אי אמון בממשלה.

When modifying an ADJ, VERB or an ADV, it is tagged ADV.
ADJ:
בשנת 1984 עלתה לשלטון מפלגת העבודה וחוללה מהפך בלתי צפוי.

VERB:
אי אפשר להפריד בין כושר גופני ליכולת ריצה.
See the discussion on אפשר:  
מה אי אפשר לעשות? ← להפריד בין כושר גופני ליכולת ריצה.
In this construction, אפשר is the root and is tagged VERB (VerbType=Mod), להפריד is the csubj and is also tagged VERB (VerbForm=Inf), and אי is advmod tagged ADV with the features Prefix=Yes and Polarity=Neg (see VerbForm, VerbType & Polarity).
 
ADV:
פונקציה חד-חד-ערכית היא פונקציה המקבלת כל ערך פעם אחת לכל היותר.

#### Abbr
Some NOUNs, PROPNs, ADJs and ADP may appear in their abbreviated forms. Note that the Lemma should be the abbreviated form, not the unabbreviated one. In case we want to be extra diligent, we can add the unabbreviated lemma using the feature Gloss under Misc Features.
  

NOUNs:
יו"ר הוועדה, ח"כ אורה נמיר, טענה כי…
The features we assign here to both are Abbr=Yes | Gender=Fem | Number=Sing. Additionally, the feature Definite=Cons is assigned to יו”ר for being in a compound (smixut) phrase.

PROPNs:
המדינות האחרות בארה"ב נהנו משגשוג חסר תקדים.  

ADJs:
הדמות התנ"כית שממנה הוא שואב השראה היא דוד המלך.

ADPs:
מעריכים שהישיבה תנוהל ע"י שלום עצמו.

#### Typo
In cases where the data has an incorrect form we will assign the feature Typo=Yes under Misc Features. This goes hand in hand with the feature CorrectForm which is used to indicate the correct spelling of a token.

#### CorrectForm
We indicate the correct form of a token, wherever the data contain an incorrect spelling. This is done under Misc Features and is combined with the feature Typo=Yes.

#### Uncertain
When we are not sure of a feature we assigned to a token, or the choice of a POS tag, we should assign the feature Uncertain=Yes under Misc Features. 


## Dependency Relations in Detail
### Core Arguments
Our annotation guidelines encompass all UD core argument relation types except for iobj (not used), since Hebrew does not have core marking for indirect objects (recipients and similar arguments are mediated by the preposition ל and are therefore attached regularly as obl. 

We also distinguish the commonly used nsubj:pass and csubj:pass for subjects of passive voice verbs.


#### nsubj

This function label is used for the syntactic subject of a clause, be it a finite verb’s subject, which typically agrees in number and gender with the finite verb, or the subject of a nominal sentence, either marked by a copula, or without an overt copula. These three most common constructions are illustrated below:

-	Finite verb subject:

tree

-	Overt copula (same analysis also with forms of היה, ADJ predicates, etc.)

tree

-	No copula:

tree

The nsubj label is also used for existential predication (יש\אין) when the existential nominal appears without a preposition (for יש את see obj, for יש ל see obl).

tree

In nested predicates where the nesting predicate is in a copula clause with its subject, two nsubj relations are possible for the same head. For example:

tree

#### nsubj:pass

This label is used for subjects of finite passives as well as predications with passive participles:

tree

tree

#### obj

The obj label is given to direct objects not mediated by a preposition, as well as definite direct objects mediated by the object marker את, which is attached to the head of the object phrase as case.

-	Direct object without את:

tree

-	Object with את:

tree

Note that pronominal objects with forms of את + suffix pronouns are analyzed in the same way:

tree

Additionally, accusative marked objects of the possessive construction are marked with the obj relation, unlike indefinite existentials which are labeled nsubj (see nsubj for יש + noun phrase).

#### csubj

The csubj relation is used analogously to the nsubj relation in cases where the subject is a clause, for example an infinitive clause, a complementizer ש clause (less common) or a plain finite clause (rare). If the predicate is a passive verb or participle, see the csubj:pass relation instead.

tree

When in doubt about complement clause (ccomp) vs. subject clause status, consider whether the corresponding question could be posed with ‘את’, or whether it could be replaced by “את זה”:

-	מה היה נדמה? שדיסני עלתה שוב על הסוס
-	*את מה היה נדמה? שדיסני עלתה שוב על הסוס
-	*היה נדמה את זה

#### csubj:pass

This relation is analogous to csubj, except that the predicate is a passive finite verb or predicative passive participle (בינוני פעול). This does not include possible ‘mediopassives’ (Voice=Mid) where an action is not truly passive, but uses a reflexive binyan to indicate benefactivity or self-interest of the agent (for example נמאס is usually not a true passive). A common test for passive as opposed to medium forms is the possibility of introducing a by-agent (על ידי…, e.g. * נמאס על-ידיי לעבוד is odd, but הוא נרצח על ידיהם is grammatical, i.e. a true passive). See the Voice feature under Morphology for more details.

 Some examples of csubj:pass:

tree

tree

tree

#### ccomp

The label ccomp is used for complement clauses, often of speech or psych verbs which take content clause arguments:

tree

This label is not used for complement clauses of nominals (e.g. deverbal nouns) which are instead labeled acl (see acl).

#### xcomp

The label xcomp (external or ‘open’ complement clause) is used for non-finite complements of verbal or adjectival predicates which structurally lack an overt subject, but whose subject is inferred to be an argument of their dependency parent. The most typical cases involve subject or object control (PRO control in classic Government and Binding theory), where the unexpressed subject of the xcomp infinitive is coreferent with the subject, or more often object of the superordinate verb. For example:

-	Direct object as external subject: (object of מכריח is the subject of לנסוע)

tree

-	Subject sharing: (subject of רצו is also the subject of להמשיך)

tree

-	Prepositional object: (the subject of ‘ללכת סביב’ is ‘מטוס’, the prepositional object of the parent verb, למנוע)

tree

Care should be taken not to label plain infinitival adverbial clauses (purpose clauses) as xcomp - these should be advcl. A common test is ability to insert כדי: 

tree

Additionally, the xcomp label is used for the secondary predicate of verbs meaning ‘become’ or similar, which do not take true objects (and are incompatible with את), for example:

tree

Note how in this example, it is not possible to say “היא נעשית את מורה”. Use of the label xcomp in such cases in UD is based on an analysis (originally from the LFG literature) which interprets ‘become’ predicates as involving a secondary predication with coreferring subjects, viz. “[she becomes - [she is a teacher]]”. Also note that this analysis is not applied to ‘become’ verbs with overt PP secondary predicates, which are analyzed as obl using the usual guidelines:

tree

### Non-Core Dependents
#### advcl

This label is used for all oblique clausal dependents, including adverbial clauses proper, whether finite or non-finite (temporal כש, locative היכן ש, purpose כדי, conditional etc.), and oblique argument clauses, due to the lack of an argument/adjunct distinction in UD (e.g. אני מחכה לכש...). As with all UD clausal dependents, attachment proceeds from head to head, from the governing predicate head to the subordinate predicate head. 

-	Plain finite adverbial clause:

tree

-	Infinitival adverbials clause:

tree

Typical advcl constructions are introduced by the following subordinators (not an exhaustive list; see also mark):

-	Conditional: אם, אילו, לו
-	Temporal: כש, כאשר
-	Causal: בגלל ש, כיוון ש, היות ש, הואיל ו
-	Purpose: כדי, כדי ש, על מנת ש
-	Concessive: למרות ש, אף על פי ש
-	Manner/means: כך ש, בלי ש
-	Extent:	ככל ש

Note that advcl is not used for infinitival complement clauses which take the xcomp relation (see xcomp).

#### advmod

This label is used for adverbial modifiers, including modifiers of VERBs, ADJ and ADV heads (the latter two typically for intensification), and, rarely, NOUNs. Note that according to UD guidelines advmod dependents may only be tagged ADV, unless they are the head of a multiword expression connected via the fixed relation (and therefore we assume the multiword expression as a whole functions as advmod).

Typical examples:
-	VERB modifier:

tree

-	ADJ modifier:

tree

-	ADV modifier:

tree

-	Less frequently, NOUN modifier - most cases involve NP modification with semantics like ‘also’, ‘only’ etc.:

tree

Note also in particular that advmod is used for most forms of negation:

tree

#### aux

This label is given to tense and modality auxiliaries of predicates, but not to impersonal modals which are themselves a main predicate. 

Tense auxiliary example:

tree

Modal auxiliary example:

tree

Note that in both of the above cases, the auxiliary is not the main lexical predicate, but rather serves to add tense or mood to a lexical predicate. This is not the case with the independent impersonal modals. Modal predicates which are not auxiliary:

tree

tree


Note that the infinitive attached to impersonal modals is typically a subject clause:

-	אי אפשר לדעת (.To know something is impossible = What is impossible? To know)

#### cop

This label is used for copulas, including tensed forms of היה as a copula (זה היה טוב), but not as a tense auxiliary (היא היתה הולכת). It is also used for pronominal copulas such as זה and הוא:

tree

Note that the copula can precede the subject, so care must be taken to identify and distinguish the subject from the copula:

tree

#### discourse

This label is used for interjections (היי), fillers (אה) and non-adverbial discourse markers (phatic כאילו). For clausal modifiers, attachment is to the clause root, but for phrasal phatic markers (כאילו, יעני) and fillers attachment is to the modified head, which usually follows the marker. For example:

-	Clausal modifier of root:

tree

-	Filler:

tree

-	Phatic adnominal dependent:

tree

It is also used for appellative particles in combination with a vocative, e.g.:

tree

#### dislocated

This relation is used for a right or left dislocated verbal argument, which is used in addition to the regular mention of that argument, as in the following example:

tree

Note that the dislocated argument is identified as the one that is farther from the verb, with the usual pattern being a heavy noun phrase being extraposed (either before the main clause, or postponed until after the verb), with the same argument being realized ‘redundantly’ as a pronoun in the canonical subject or object position. If the pronoun and lexical NP are realized on opposite sides of the VERB, we assume that the dislocated argument is the lexical one, which is usually set apart by a comma in writing. For example:

-	מוטי, הכרתי אותו (מוטי=dislocated)
-	הוא נחמד, מוטי (מוטי=dislocated)
-	אותו לא הכרתי, את מוטי (מוטי=dislocated)

Also note that in the bare hanging topic construction, the topic head is labeled dislocated  despite not bearing overt marking of the same function (as in the repetition of את in the last example). For example:

-	מוטי, אחותו נחמדה (מוטי=dislocated)

Here the dislocated mention is not explicitly marked as a possessor, though the second mention in canonical position serves a possessive function.

#### expl

The expl relation marks the placeholder expletive זה when it takes the position of a dislocated argument, usually a subject clause labeled csubj.

tree

Note that this construction can be identified by the possibility of omitting זה:

-	לא נכון שבישראל הוא היה סרסור

#### mark

This label is used for the subordinator (a.k.a. Complementizer, tagged SCONJ) in subordinate clauses, both finite and infinitival, most commonly ש or כי and multiword expressions (with fixed) headed by ש. It is attached to the subordinate clause head (usually a VERB or nominal predicate).

-	Finite argument clause:

tree

-	Infinitival adverbial clause:

tree

-	Multiword example (see also fixed):

tree

#### obl

This label is used to attach dependent nominals of verbs, adjectives and adverbs which have a preposition (i.e. a case dependent), as well as dependents of nouns if they are actually modifying an entire nominal sentence, rather than the noun in the narrow sense. The most common cases of prepositional modifications to verbs and adjectives are analyzed as follows:

-	PP modifiers of VERB:

tree

-	PP modifiers of ADJ/ADV:

tree

The more unusual case of modifying a nominal predication follows the pattern in the following example. Note that here the modifier is not actually modifying the noun, as in the subsequent example, which uses the nmod relation:

-	The obl relation attached to a predicate NOUN, indicating that the modifier belongs to the predication itself (‘being important’ is according to mulsim traditions; there is no noun phrase “בעל חשיבות גם לפי חלק מהמסורות...”)

tree

-	Normal nmod of a NOUN which happens to be a predicate (i.e. there is a noun phrase “מחווה למדענית”)

tree


To distinguish between these last two variants (which are only confusable for PP modifiers of copula predicate nouns), we can consider the possibility of movement for the PP modifier. Compare:

-	* למדענית שמו של השבב החדש הוא מחווה
-	גם לפי חלק מהמסורות המוסלמיות הסלע המזוהה כאבן השתייה הוא בעל חשיבות

The infelicity of the first example demonstrates that the modifier belongs in a single noun phrase with the noun מחווה, and therefore the usual adnominal nmod relation should be used.

Additionally, obl is used for the possessor in predicative possessives when the possessor is marked with ל (this is in fact not an exception, but rather an application of the normal rule that prepositional dependents of VERBs are labeled obl). The direct object, marked by את, is labeled obj (but note that indefinite plain existentials are labeled nsubj; see nsubj):

tree

#### obl:tmod

This label is used for temporal modifiers of VERBs, ADJ and ADV which are not mediated by a preposition (e.g. extent: dance two hours, temporal: arrived 1992, etc.). Typical items taking this label include יום, שנה, דקה, שעה, שבוע, חודש, שנייה and year numbers or other date expressions without a preposition.

Examples:

-	Time of predicate:

tree

-	Extent:

tree

-	Temporal frequency:

tree

Note that temporal modifiers with prepositions are regular obl, and that temporal moidifiers of nominals (NOUN, NUM) are nmod:tmod:

tree

#### vocative

This label is used to denote direct appellation of an addressee, either in conjunction with an imperative, or as a non-argument mention of the addressee. For example:


tree

Note also that appellative discourse particles tagged INTJ (יא, הו) are attached to the vocative via the discourse relation.

### Nominal Dependents
#### acl

The label acl is used for all adnominal clauses that are not nominal clauses, including infinitive and finite clauses. Object clauses of deverbal nouns are also labeled acl (and not ccomp):

-	Adnominal infinitive clause:

tree

-	Finite adnominal complement clause, also labeled acl (and not ccomp, per UD guidelines)

tree

This label is only used if there is no possibility of introducing a resumptive pronoun, which would indicate that this is a relative clause.

#### acl:relcl

This label is given to the head (predicate) of a relative clause, whether it is introduced by an explicit relative pronoun (ש, אשר, ה labeled mark) or not (zero relative).

-	Explicit relative marker:

tree

-	The same, but note the non VERB predicate:

tree

-	Zero relative (note the possibility of inserting ש):

tree

A special construction which deserves mention is free relatives. In free relatives, a single word (the node word, usually an interrogative pronoun) serves a double role in both the main clause and a subordinate relative clause. In these cases, both functions cannot be annotated in a simple tree (though they are represented in Enhanced UD trees), and we therefore prioritize the matrix clause function, while allowing the word to govern the subordinate clause as acl:relcl. The following examples illustrate this:

-	Node word in predicate position:

tree

-	Node word in object position:

tree


To understand this analysis, we can consider what would happen if both functions of the node word would be separated into two words:
-	אז זה בדיוק הדבר שעשיתי אותו
-	לדעת את הדרך שבה יפעל וייראה התוצר הסופי

These paraphrases illustrate the existence of the two functions: in the first example, מה is replace by דבר (realizing the predicate function of מה) and אותו (its function as the object of עשיתי). Similarly for איך, the paraphrase reveals that it is both the object of ‘knowing’ and the oblique manner PP of ‘how the product will work’.

Note that this analysis does not apply to polar object clauses (i.e. “whether” clauses), marked by ‘האם’ or ‘אם’, which are seen as ccomp (i.e. אני יודע אם הוא בא is analyzed like אני יודע שהוא בא).

#### amod

This is the label given to adjectival modifiers of nouns, e.g.:

tree

Note that when the NP is definite, the second agreement article is attached to its adjective as det.

#### appos

This label is used for appositional nouns, which expand on an existing noun without adding an additional argument or participant to the clause (the appositive fills the same role as the noun it expands on). Apposition is expected to be reversible, i.e. we can say NP1 NP2, or NP2 NP1, though in practice this will sometimes sound odd. 

Example:

tree

Note we could say:
-	בן היישוב שנפטר לאחר מאבק ממושך במחלת הסרטן, חן שמיר

In cases of double or triple apposition, all appositive heads are attached to the first, argument function bearing head. In:

-	הרמטכ"ל, אביב כוכבי, איש קרית ביאליק…

The appositive heads אביב and איש would both be dependents of רמטכ”ל.

The following expressions are all examples of appos:

-	העיר חיפה
-	התוכנה בבילון
-	המילה “פופיק”
-	השנה 1990
-	האדון כהן (only with a definite article; אדון כהן would be flat)
-	השחקנית פאר

Note that common titles and appellations such as מר, גברת are analyzed as part of the name, tagged PROPN and taken as the first part of a flat relation if and only if they occur without an article. For items that can occur with or without an article, such as אדון כהן vs האדון כהן, the construction without an article is taken to be flat, but the construction with the article is taken to be appos.

The “city, state” construction is also conventionally analyzed as appos, in keeping with other UD languages:

-	בוסטון, מסצ'וסטס
-	פראג, צ'כיה

#### case

This label is used to mark prepositions, tagged ADP, which are attached to a nominal, indicating the lexical type of a prepositional phrase. Note that if the head of the phrase is verbal or forms a complete nominal predication, what would otherwise by a preposition will be tagged SCONJ and attached as mark instead. 

Compare:

-	Plain nominal head with the preposition בלי (tagged ADP):

tree

-	Nominal is actually part of a predication marked with the SCONJ complementizer בלי:

tree

For multiword prepositions, the POS tags can be ignored (i.e. they do not all need to be ADP) and the first word in the fixed chain is always the one attached as case regardless of any opinions about the possible internal structure of the multiword preposition.

tree

#### det

The label det is given to determiners of nominal heads, most often the article ה (except when used as a relative marker, see mark), and some other quantifiers, which crucially do not form a separate referring expression (see below for counterexamples). The most common Hebrew determiners are: 
-	ה, זה
-	כל, כמה, הרבה
-	שום, אף
-	אותו (אותו אחד)

Examples

-	Simple article:

tree

-	Compound article: (סמיכות)

tree

-	Quantifier:

tree

-	Postposed demonstrative:

tree

Agreement articles on attributive adjectives are also attached as det to their respective adjectives:

tree

This also applies to postposed demonstratives with an agreement article, resulting in ‘det of det’:

tree

Orthographically unexpressed articles are not attached via det, and their prepositions are attached via case as usual, though the morphological features of the ADP will express Definite=Def in such cases (see Definite). For example:

tree

Finally note that partitive or other referring expressions with a determinative sense are not labeled as det, in particular when they form part of a prepositional phrase, both the head and dependent in which could be referred back to with a pronoun. Compare:

-	No internal preposition, single referring expression:

tree

-	Preposition phrase, two referring expressions:\

tree

In the first example, the head is מירוצים, and only one referring expression is formed (a pronoun הם could only refer back to the entire כמה מירוצים, which creates exactly one participant in the sentence). In the second example, the head is כמה, which has an nmod prepositional phrase dependent, מהמירוצים. In this latter case a subsequent pronoun הם could refer back either to all races (המירוצים), or only to several races (כמה מהמירוצים), indicating that we are dealing with two full (but nested) NPs.

#### nmod

The label nmod is given to prepositional phrase dependent nominals of other nominals (adnominal PPs). These are prepositional phrases which are embedded in a noun phrase, for example:

tree

Note that it is not impossible to have an obl dependent of a NOUN, specifically when the NOUN is the predicate of a nominal sentence (see obl for more discussion):

tree

#### nmod:poss

This is a special subtype of nmod which is given to possessor phrases of two kinds: prepositional possessors with a case dependent של, and suffixal clitic possessor pronouns.

-	Prepositional possessor:

tree

-	Clitic possessor:

tree

This also applies for cases that are not really possessive, but contain של, like מהירות של 50 קמ”ש.

#### nmod:tmod

This label is used for temporal modifiers of nominals, not introduced by a preposition (for which we use the regular nmod). For example, years in dates are nominal modifiers, but not introduced by a preposition:

tree

#### nummod

This label is used for numerical dependents which indicate the quantity of a head nominal, regardless of how the numeral is expressed (spelled out, Arabic numeral, Roman numeral, Hebrew letters, etc.). 

tree

Numbers which commonly create a construct state with their enumerated noun are still analyzed as nummod:

tree

As compound numbers are not analyzed using the compound relation, in some cases, chains of nummod may be possible, for example:

tree

Note that not all Arabic numeral tokens are nummod.

tree

### Other
#### cc

This label is given to explicit coordinators, such as ו, או, אבל etc. They are attached the coordinate conjunct head following them, i.e. pointing backwards.

tree

#### compound:affix

This label is used for a closed set of prefixes forming complex words, but only when they are spelled apart. The allowed prefixes are:

-	בין, תת, דו, חד, תלת, פרו, קדם, אנטי, סופר, נאו, מולטי, מיני, אקס, כלל, פוסט, טרום 

Example:

tree


Note that the POS category of the prefix token is determined by the head word’s POS: compound:affix modifiers of NOUN are tagged ADJ, while compound:affix modifiers of ADJ/ADV are tagged ADV..

#### compound

This label (formerly called compound:smixut, now shortened to compound) is given to all non-head construct state dependents (סומך). For example:
-	Simple construct state:

tree

-	Nested construct state:

tree

#### conj

This label is given to the head of each coordinate conjunct after the first in a coordination. All non-initial conjuncts are attached to the initial one, creating a ‘fountain’ shape, rather than a chain - in “A, B and C”, both B and C will be attached to A. Any coordinating conjunctions will be attached to their conjunct heads as cc. Examples:

-	Plain coordination

tree

-	Multiple conjuncts (‘fountain’ shape)

tree

It is also possible to have nested coordination, in which case nested conjuncts may attach to the second conjunct (because they are not coordinate with the first in the chain):

tree

#### dep

A dependency can be labeled as dep when it is impossible to determine a more precise relation. This may be because of a weird grammatical construction, or a construction for which none of the existing guidelines apply, with the understanding that we may want to go back and devise guidelines for such cases. The use of dep should be avoided as much as possible.

#### fixed

This label is given to multiword fixed expressions, and the dependency always proceeds from the first word of the expression to all subsequent words in a ‘fountain’ shape.

tree

The following fixed expressions of 2-4 tokens are recognized:

-	**Two tokens**:
    -	Nouns

        אב טיפוס 
        שח מט       
    -	Prepositional
 
        אחרי ש 
        אל תוך 
        ב העדר 
        בהתבסס על 
        ב מהלך/מסגרת/פי
        בניגוד ל 
        חוץ מ 
        ל טובת/משך 
        לבד מ 
        למעלה מ 
        מ אז/זה/לפני
        מ זה 
        מעל ל 
        עד כדי/כה/ל/לפני/מאוד
        על אודות/אף/גבי/ידי/כן/פי/פני/רקע
        תוך כדי  

    -	Conjunctions
        אם כי 
        אף/בגלל/כדי/כפי/כשם/לפני/למרות/מאחר/מפני/משום/עד/כך ש 
        בגלל ש 
        היות ו 
        על מנת

    -	Coordinations
        ו אולם/אילו 
        ו כדומה/כו
    -	Other
 
        אחת היא 
        אי פעם 
        אין סוף 
        הוקוס פוקוס 
        החל ב/מ
        זה כבר/עתה
        יותר/פחות מ/מדי
        כל כך 
        לא אחת 
        לא כבר 
        סוף סוף 
        קודם/ראשית כל  


-	**Three tokens**:
    -	Coordinations
 
        אחת ו לתמיד
        באמת ו בתמים
        בראש ו בראשונה
        די ו הותר
        ו כך הלאה
        ו תו לא
        זאת ו עוד
        כהנה ו כהנה
        כלל ו כלל
        כלל ו עיקר
        פה ו שם
        מאז ו מתמיד
        מעל ו מעבר
        מעתה ו הלאה
        שוב ו שוב
        תיכף ו מיד
        כך או אחרת
        כך או כך
        פחות או יותר 

    -	Prepositional
 
        ב בת אחת
        ב דומה ל
        ב שונה מ
        ב דרך אגב/כלל
        ב המשך ל
        ב השוואה ל/אל
        ב התאם ל
        ב התייחסות/יחס/קשר ל/אל
        ב יחד עם
        ב כל זאת
        ב כלל זה
        (ב) נוסף ל/על
        ב תמורה ל
        ל עתים (לא) קרובות/רחוקות/תכופות (מדי/יותר)
        מ חוץ ל
        מ סביב ל
        עד כדי כך 
    -	Adverbial
 
        יותר מ כל
        יחד עם זאת
        יתר ה מידה
        יתר על כן
        יתרה מ זאת/זה
        כל כך הרבה
        קודם ל כל
        קודם ל כן
        קרוב ל וודאי 
    -	Other
        זה) (לא) מ כבר)
        כל אימת ש
        לא בלבד ש
        לא זו בלבד
        לא מ כבר
        פעם אחר פעם

-	**Four tokens**:
        יתר על ה מידה
        כ/מ/מדי פעם ל/ב פעם (=מפעם לפעם, כפעם בפעם…)


#### flat

This label is given to multiword names without a clear head structure (for example first+last name), tagged PROPN, with a ‘fountain’-like structure, in which the first token governs all subsequent tokens. Examples:

tree

Compositional names should not receive this analysis, and are analyzed based on the underlying syntax, though the PROPN POS tags still indicate that they are names:

tree

Non-compound complex names do use the flat relation, for example:

-	המכללה תל חי

In this case we can tell that the relation is not compound due to the definite article on the first word, and we connect מכללה to תל as flat (תל חי itself is compound). Note that in such cases the flat expression is itself a name, not an apposition (compare the following cases, which are appos: העיר חיפה, התוכנה בבילון, המילה “פופיק”, השנה 1990)

Common titles and appellations such as מר, גברת are analyzed as part of the name, tagged PROPN and taken as the first part of a flat relation if and only if they occur without an article. For items that can occur with or without an article, such as אדון כהן vs האדון כהן, the construction without an article is taken to be flat, but the construction with the article is taken to be appos.

#### goeswith

This label is given to incorrectly split-off tokens, which should actually be one word in correct orthography. It always proceeds from the first token-part to all subsequent parts. For example:

tree

#### orphan

This rare dependency replaces the relationship between an elided coordinate predicate with multiple dependents and its secondary dependents, by promoting its primary dependent to take its place. 

Based on the UD rules of promotion (see General Principles: Elliptical Promotion), when an elided head has multiple dependents, its primary dependent is taken to fill the role of the predicate (usually conj). However when there are multiple dependents, it is odd, for example, for a promoted conj subject to govern a direct object. In such cases, the secondary dependents are attached to the promoted token as orphan. For example:

tree

In this example, the elided second repetition of the VERB זקוקים triggers the promotion of its subject, משפחותיהם, which is now conj to the first זקוקים. The adverbial clause head לתמוך cannot be attached as such to משפחותיהם, since it is an adverbial clause which should attach to the missing VERB. The indirect relationship between the primary dependent (משפחותיהם, which is the highest ranked dependent of the ellipsis) and the secondary dependent (לתמוך) is therefore expressed via the orphan relation.

#### parataxis

The parataxis relation is used for two distinct scenarios: when two independent trees, usually full sentences, stand next to each other in the same orthographic sentence without an explicit coordination, and in the case of parentheticals, which do not belong to the superordinate syntactic structure. 

-	Coordinating (sentential) parataxis:

tree

-	Parenthetical parataxis:

tree

Note that the adverb אז can appear as a loose coordinator between sentences, but since it is not analyzed as cc, this case also triggers a parataxis analysis:

tree

Note that unlike coordination, parataxis is not considered transitive, and does not form ‘fountain’ shaped dependencies. Instead, multiple paratactic structures are arranged in a ‘chain’ shape, i.e. S1 -> S2 -> S3, where the head of each paratactic sentence is joined to the next (and not all to the first).

#### punct

This label is given to any and all tokens tagged as PUNCT (see PUNCT), which are generally punctuation marks which have no pronunciation (for pronounced symbols, see SYM). quoteאנרAttachment of punctuation in Universal Dependencies follows the following principles, but need not worry annotators due to its automation (see below):

1.	punct tokens always attach to content words (except in cases of ellipsis) 
2.	punct can never have dependents 
3.	punct separating coordinated units is attached to the following conjunct (to B in A, B)
4.	punct preceding or following a dependent unit is attached to that unit.
5.	Within the relevant unit, punct is attached at the highest possible node that preserves projectivity (i.e. avoiding crossing edges)
6.	Paired punctuation marks (e.g. quotes and brackets, sometimes also dashes etc.) should be attached to the same word unless that would create non-projectivity. This word is usually the head of the phrase enclosed in the paired punctuation.

Because punctuation attachment is so robotic, we generally apply the official Udapi FixPunct block to parsed and also manually treebanked output, in order to avoid meaningless variation in punctuation attachment. As a result, annotators can generally leave punctuation marks as attached by the parser, though care must be given to token mistagged as punctuation, or incorrectly not tagged as punctuation.

#### reparandum

The label reparandum is given to the highest possible head of an aborted phrase (e.g. due to disfluency, typos, etc.) and is attached to the head word or head of phrase seen as its replacement. For example in a plain repetition, the first instance is considered aborted, and the second instance is its replacement.

tree

If an aborted phrase has multiple words, they are analyzed as usual until the head of the aborted phrase is reached, which is then attached as reparandum.

##### root

The root label is a special label reserved for the unique dependency root of each sentence. There is exactly one root per sentence, and its head is always set to 0 (not dependent of any other token). The root token is generally the head of the predicate phrase (predicate VERB, ADJ, head NOUN of a nominal predicate, etc.) or the highest node in a non-predicate fragment sentence (e.g. head NOUN in a plain NP sentence, or the INTJ token in an utterance consisting solely of an interjection.

Nesting predicates in which a copula construction predicates a second predication also take the normal top level matrix VERB (or other predicate word) as their root. This can lead to situations in which a single root token is the predicate of two different subjects, from the nesting and nested predication, for example:

tree

### Open Questions
Please list new annotation questions in this section AND add a comment, mentioning each person who should chime in on this using “@” (so those people get a notification to answer):
-	Example: should a Hindi word in a sentence be tagged as X? 

#### Aspect

●	In the new Wikipedia data, we have a new morphological feature, which I assume is added by the newly improved automatic parser. The feature is Aspect=Prog, akin to the English progressive tenses. Here’s the relevant page on the UD website.

An example for this feature is the following construction, where the beinoni is accompanied by the past derivations of היה as AUX.


מגדה הייתה חברתה ללימודים של ליזה, אחותו של ארלוזורוב, והייתה מיודדת גם עם 
חיים ארלוזורוב, והרצח נועד לטשטש את היחסים הקרובים שהיו בינו למגדה.

The beinoni in this instance actually has Tense=Past, instead of the usual Tense=Pres, presumably to relay the past progressive meaning.



18	הייתה	היה	AUX	AUX
	Gender=Fem|Number=Sing|Person=3|Polarity=Pos|Tense=Past	19	aux	_	_

19	מיודדת	יודד	VERB	VERB	Aspect=Prog|Gender=Fem|HebBinyan=PUAL|Number=Sing|Person=3|Tense=Past|
VerbForm=Part	5	conj	_	_

This is not the best example, since מיודדת is probably better tagged as ADJ with the lemma מיודד. But assuming we leave it as a beinoni VERB, is this a feature we should add from now on to such constructions?

#### Dates
●	Why do we use the obl deprel from day to month in Hebrew Dates, like the one below? Is it solely because of the preposition? Shouldn’t nmod be more suitable, considering the day is not strictly a numeral, but a shorthand for “the 25th day”, that is, a nominalized numeral?

המאמר פורסם בעיתון "מאמענט", שראה אור בוורשה שבפולין, ב-25 ביוני 1933.

19	ב	ב	ADP	ADP	_	21	case	_	_
20	-	-	PUNCT	PUNCT	_	21	punct	_	_
21	25	25	NUM	NUM	_	11	obl	_	_
22	ב	ב	ADP	ADP	_	23	case	_	_
23	יוני	יוני	PROPN	PROPN	_	21	obl	_	_
24	1933	1933	NUM	NUM	_	21	nmod:tmod	_	SpaceAfter=No


## Appendix: Notes on HTB Conversion

The following notes document the main changes undertaken in the HTB for use as a training basis in the tools used to produce new IAHLT Hebrew data.
### Tokenization
During conversion, ‘inserted’ tokens, such as unexpressed definite articles after prepositions, were removed, and replaced with a definiteness feature where relevant (see Removal of reconstructed articles and של/את above).

#### Notes:
-	The majority of cases of inserted tokens have leading/trailing underscores as shown above – these were relatively easy to identify, though fixing them was quite complex in terms of realigning tokens to supertokens
-	In some cases, the fused token takes the lemma form of the pronoun (e.g. אנחנו), but not always (sometimes נו) - normalized to match text form (נו)
-	A further subset of cases was identified with no leading/trailing underscores – these were recognized based on implausible sequences of POS tags/lemmas (e.g. supertoken containing NOUN followed by של and PRON)

### Morphology
-          Added definiteness and article feature to elided article subtokens:

#### OLD:
22-24    	לעובדים  	_           	_           	_             	_           	_           	_           	_             	_
22         	ל           	ל           	ADP     	ADP             	_           	24         	case      	_             	_
23         	ה_         	ה           	DET     	DET             	Definite=Def|PronType=Art    	24             	det        	_           	_
24         	עובדים    	עובד       	NOUN  	NOUN             	Gender=Masc|Number=Plur    	20             	nmod    	_           	_

#### NEW:
22-23    	לעובדים  	_           	_           	_             	_           	_           	_           	_             	_
22         	ל           	ל           	ADP     	ADP             	Definite=Def|PronType=Art         	23         	case             	_           	_
23         	עובדים    	עובד       	NOUN  	NOUN             	Gender=Masc|Number=Plur    	20             	nmod    	_           	_
24         	מתחת     	מתחת     	ADP     	ADP             	_           	26         	case      	_             	_

Note that adding the features on prepositions, rather than nouns, is consistent with treatment of regular definiteness, since where a normal ה is present, definiteness is marked on the article, not the noun (so we should not mark it on the noun here either).

#### Other changes:
-	Poss=Yes was added for fused possessive pronouns, added this since it can be automated easily.

#### Lemmatization
-	Added missing lemmas for items formerly lemmatized “__” (e.g possessed אח)
-	Normalized final letter lemmas like ח"ך -> ח"כ and bizarre wrong lemmas (e.g. הכול was consistently lemmatized to הכיל in HTB…)
-	Lemmatized חצי to חצי (was consistently: חץ)
-	Lemma of הכול was consistently wrong as ‘הכיל’, fixed to be itself
-	All cardinal numbers lemmatized to unmarked feminine form
-	All demonstrative pronouns lemmatized to זה except זהו/זוהי (lemma=זהו) and הללו (lemma=הללו, since it is not segmented, but actually corresponds to הזה, which is segmented, and not to זה)

#### POS tags
-	HTB oddly annotates the interrogative pronouns מי and מה as ADV, except for the forms מיהו and מהו which are lemmatized to מי and מה but tagged PRON. These forms receive normal grammatical function labels, such as nsubj or obj, and it is not clear why they should be considered adverbs – they were therefore changed to be PRON whenever they do not have the deprel advmod.
-	Tagged חצי as NUM, not NOUN.
-	ה used to introduce a relative clause is mark, so it cannot be tagged DET; changed to SCONJ as per the UD guidelines.
-	Some questionable POS tagging conventions from HTB were kept (in some cases since they are consistent and difficult to change automatically):
-	Nominalized participles are often tagged as VERB, for example consistently so for חולים in בית חולים - the VERB tag was retained and also used in new gold data for consistency with the original HTB decision, but this may be something to review in the future. In new data, lexicalized nominalized participles for which HTB does not have a convention were generally tagged as NOUN.
-	Copulas in HTB are often VERB, which is invalid in current UD validation, so they were changed to AUX. However, for the forms הוא, היא etc., it seems more plausible to tag them as PRON, which is also allowed as a copula POS tag, so this practice was changed to PRON. 

#### Dependencies
-	Fixed recurring invalid attachment to auxiliaries (should connect to VERB), e.g. for mark:

tree

-	Reattach cc dominated by cc to intervening parent of conj (HTB conversion error?)

tree

-	Change PRON advmod to nmod or obl as appropriate

tree

-	Change אחד, חלק and similar words labeled as PRON/det (which is invalid) to be the head of  the phrase (note this is also more correct for entity recognition and agreement, since the phrase is singular and both the ‘אחד’ and the plural noun are separate referring expressions (relevant also for entity tagging)

OLD: (invalid)

tree

NEW:

tree

#### Removal of some subtypes
As discussed, case subtypes such as case:gen and case:acc for של and את respectively were removed, as well as the subtype mark:q for האם and שמא.

#### Addition of passive with nsubj:pass
This subtype was added based on the morphological Voice feature of the head of an nsubj relation.

Corrupted text
HTB seems to have an odd inversion of textual numbers, spelled ‘right to left’:
28-29   ב01  	_      	_      	_      	_        	_      	_      	_      	_
28    	ב      	ב      	ADP	ADP	_        	30    	case 	_      	_
29    	01    	01    	NUM   NUM   _        	30    	nummod      	_      	_
30    	אלפים   אלפים   NUM   NUM        	Gender=Masc|Number=Plur  27        	obl   	_      	SpaceAfter=No
Corrected these cases to:
27-28   ב10  	_      	_      	_      	_        	_      	_      	_      	_
27    	ב      	ב      	ADP	ADP	_        	29    	case 	_      	_
28    	10    	10    	NUM   NUM   _        	29    	nummod      	_      	_
29    	אלפים   אלפים   NUM   NUM        	Gender=Masc|Number=Plur  26        	obl   	_      	SpaceAfter=No
 Opened issues in UD_Hebrew:
https://github.com/UniversalDependencies/UD_Hebrew-HTB/issues/26 
But the correction was heuristic and based on the supertoken text – this might be worth letting someone go over manually/by comparing with the original TB, since some inverted numbers might be present which were not part of supertokens (so there is no record of their correct textual representation in the data).
Similar problems show up with mangled final letters and some other inserted tokens which are incorrectly reconstructed, opened issues with examples here:
https://github.com/UniversalDependencies/UD_Hebrew-HTB/issues/27
https://github.com/UniversalDependencies/UD_Hebrew-HTB/issues/28 
Data fixed heuristically in our new version (fixed manually for the cases in issue #28).
