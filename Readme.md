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

