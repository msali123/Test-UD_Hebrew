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
####ADJ or NOUN?

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
        <td></td>
        <td>Feature</td>
        <td>Values</td>
        <td>Relevant POS</td>
    </tr>
    <tr>
        <td>pronominal type</td>
        <td>PronType</td>
        <td>ArtDem Emp Ind Int Prs</td>
        <td>ADP, DET (ה) PRON</td>
    </tr>
    <tr>
        <td>numeral type</td>
        <td>NumType</td>
        <td>Card Ord</td>
        <td>NUM (Lemma=Fem) ADJ (Lemma=Masc)</td>
    </tr>
    <tr>
        <td>possessive</td>
        <td>Poss</td>
        <td>Yes</td>
        <td>PRON (clitic gen)</td>
    </tr>
    <tr>
        <td>reflexive</td>
        <td>Reflex</td>
        <td>Yes</td>
        <td>PRON</td>
    </tr>
    <tr>
        <td>abbreviation</td>
        <td>Abbr</td>
        <td>Yes</td>
        <td>NOUN, PROPN, ADJ, ADP</td>
    </tr>
    <tr>
        <td>affixation</td>
        <td>Prefix</td>
        <td>Yes</td>
        <td>ADJ (modifying NOUN) ADV (modifying ADJ,ADV,VERB)</td>
    </tr>
    <tr>
        <td>wrong spelling</td>
        <td>Typo</td>
        <td>Yes</td>
        <td>Any</td>
    </tr>
    <tr>
        <td>correct form</td>
        <td>CorrectForm</td>
        <td>free value</td>
        <td>Any (if Typo=Yes)</td>
    </tr>
    <tr>
        <td>uncertainty</td>
        <td>Uncertain</td>
        <td>Yes</td>
        <td>Any</td>
    </tr>
    <tr>
        <td>gender</td>
        <td>Gender</td>
        <td>Fem Masc  Fem,Masc</td>
        <td>AUX, ADJ, NOUN, NUM, PRON, VERB</td>
    </tr>
    <tr>
        <td>number</td>
        <td>Number</td>
        <td>(Dual) Plur Sing</td>
        <td>AUX, ADJ, NOUN, NUM (only for clear Number morphology), PRON, VERB</td>
    </tr>
    <tr>
        <td>case</td>
        <td>Case</td>
        <td>Acc   Gen</td>
        <td>ADP (את), PRON (clitic acc) ADP (של), PRON (clitic gen)</td>
    </tr>
    <tr>
        <td>definiteness</td>
        <td>Definite</td>
        <td>Cons   Def</td>
        <td>ADJ, DET, NOUN, NUM (CS form) ADP, DET (ה) PRON (as determiner)</td>
    </tr>
    <tr>
        <td>verbal form</td>
        <td>VerbForm</td>
        <td>Inf Part   Vnoun</td>
        <td>VERB  VERB (infinitive absolute in adverbial clauses)</td>
    </tr>
    <tr>
        <td>verbal type</td>
        <td>VerbType</td>
        <td>Cop Mod</td>
        <td>AUX, VERB</td>
    </tr>
    <tr>
        <td>aspect</td>
        <td>Aspect</td>
        <td>Prog</td>
        <td>VERB (only beinoni in היה+בינוני and in past progressive meaning)</td>
    </tr>
    <tr>
        <td>mood</td>
        <td>Mood</td>
        <td>Imp   Irr</td>
        <td>VERB (Imperative)  VERB (only beinoni in היה+בינוני and in contra-factual meaning)</td>
    </tr>
    <tr>
        <td>tense</td>
        <td>Tense</td>
        <td>Fut Past Pres</td>
        <td>VERB, AUX</td>
    </tr>
    <tr>
        <td>voice</td>
        <td>Voice</td>
        <td>Act Mid Pass</td>
        <td>VERB</td>
    </tr>
    <tr>
        <td>polarity</td>
        <td>Polarity</td>
        <td>Neg Pos</td>
        <td>ADV, AUX (cop), PRON (cop)</td>
    </tr>
    <tr>
        <td>person</td>
        <td>Person</td>
        <td>1 2 3</td>
        <td>AUX, PRON, VERB</td>
    </tr>
    <tr>
        <td>verbal template</td>
        <td>HebBinyan</td>
        <td>HIFIL PAAL PIEL HUFAL PUAL HITPAEL NIFAL</td>
        <td>VERB</td>
    </tr>
</table>

Adapted for Hebrew from Marie-Catherine de Marneffe, Christopher D. Manning, Joakim Nivre, Daniel Zeman; Universal Dependencies. Computational Linguistics 2021; doi: https://doi.org/10.1162/coli_a_00402

### Detailed Table of features by POS tag

TABLE TO BE ADDED

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

