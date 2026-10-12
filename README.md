# FitFindr

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, every
> command, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> python app.py listings --full -n 6      # read the data (Milestone 1)
> python app.py fields                    # what you can filter on
> python app.py ask 'vintage graphic tee under $30'
> ```
>
> All three tools are stubs, so that last command will do nothing useful yet.
> That's the starting position.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     This is your submission. Fill each section in as you finish the milestone
     it belongs to — don't leave it all to the end.

     Unit 3 asks for the first five sections. Unit 4 adds the five below them.
     Leave the unit 4 sections alone until then; they're here so you know
     what's coming.

     Everything is pasted as TEXT. No screenshots, no images, no video links.
     A typed block of output gets full credit; a picture of the same output
     gets none.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 3 — THE BUILD ═══════════════════════ -->

## What This Does
This project allows user to inquire on clothes from a set of item listings of different categories, sizes, and styles. The listing includes tops, bottoms, outerwear, and shoes providing affordable list of clothes in the listings. Apart from the listings this project provides ouftfit suggestions and examples of fits that combine well together. 
<!-- Three or four sentences: what a user asks for, and what they get back. -->



---

## Tool Inventory

<!-- Four lines per tool. This is worth 2 points and it's the single most
     common place students lose them.

     "Returns a list" earns NOTHING. The description has to say what is IN
     the list.

     The empty case isn't optional either — it's the thing your loop branches
     on, and if you don't decide it here you'll discover it as a crash in
     Milestone 5. -->

### `search_listings`

- **What it does:** Searches the `listings.json` file for clothings and accessories, so the most relevant items are provided.
- **Inputs:** The `description` (str), `size` (str), `max_price` (float)
- **Returns:** A `matched` (list[dicts]):: This is a reversed, sorted by score, list of dicts each with `id` (str), `score` (float), `price`(float) (inclusive or not), and a dictionary called `listing`. The `listing` dictionary has these fields: `id` (str), `title` (str), `description` (str), `category` (str), `style_tags` (list(str)), `size` (str), `condition` (str), `price` (float), `colors` (list(str)), `brand` (str), and `platform` (str).
- **When it has nothing:** Returns the first result (most relevant) searched listings that match the description then run `suggest_outfit` with it, otherwise, returns an error string in `session["error"]` when the tool returns an empty list `[]`.

### `suggest_outfit`

- **What it does:** When given a thrifted item and the user's wardrobe, suggest one or two outfits.
- **Inputs:** A `new_item` a listing dict - the item the user is considering, `wardrobe` a wardrobe dict with an `items` key holding the list of items. **It may be empty** . The `new_item` has fields each with an `id` (str), `name` (str), `category` (str), `colors` (list(str)), `style_tags` (list(str)), and `notes` (str or null). The `wardrobe` has these same fields but inside the key `items` array which has a list of dicts.
- **Returns:** A `response` str of the generated suggestion of 1 or 2 outfits to combine `new_item` (dict) with other items from the user's existing `wardrobe` (dict). All pieces in the outfit from the wardrobe are explicitly mentioned. 
- **When it has nothing:** Returns a generated suggestion str, like a personal fashion stylist giving helpful and clear suggestions of at least 2 outfits, otherwise, if the parameters are empty then errors that list is empty and needs to be filled with content from `search_listings`. If generated suggestion fails then returns str of generic advice in `agent.py`'s `session["outfit_suggestion"]` for the `new_item` styling to be paired with neutral basics and complementary layers. 

### `create_fit_card`

- **What it does:** Writes a short caption about what someone would actually post on social media about what they found while thrifting.
- **Inputs:** A `outfit` str of the outfit suggestion from `suggest_outfit` and `new_item` the dict of the item they found. The `new_item` dict has these fields: `id` (str), `title` (str), `description` (str), `category` (str), `colors (str)`, `style_tags` (str), and `notes` (str).
- **Returns:** Returns a `response` str of generated captions for the outfit in 2-4 sentences, with the item name, price, and platform being specifically mentioned. The generated str includes details about the vibe that sounds like a real person sharing a thrift haul post on social media, not a store ad.
- **When it has nothing:** Returns a generated caption str in session["fit_card"] otherwise, if parameters are empty then errors that they need to be filled with content from `suggest_outfits`. If generated caption fails and is empty, it returns a generic caption with the item `title` (str), `price` (str), `platform` (str), and a short description.

---

## Planning Loop

<!-- Your branch rule, stated as a rule — the condition AND both paths — plus
     the file and function that holds it.

     Like this:
       "If search_listings returns an empty list, put a message in the session
        and stop. Otherwise take the first result and go to suggest_outfit."
        — agent.py::run_agent

     The grader checks your code against what you claim here, so the file and
     function have to be real. -->

**Branch rule:** If `search_listings` in `tools.py` returns an empty list a message is given to session and it stops with suggestions to add a price cap, search terms, and use better sizes. Otherwise take first result and go to suggest_outfit.

**Where it lives:** `tools.py::search_listings`

**How the query is parsed:** <!-- regex, string splitting, or asking the model — say which -->The query is parsed using regular expressions with "under", "below", "max", and common sizes (xxs, xs, s, m, l, xl, xxl, w) are extracted. The `description` variable replaces those two variables with empty strings and sets the remainder to `description`.

**What moves through the session:** <!-- which fields, in what order -->The output of `search_listings` is returned as a dictionary with the fields `id`:str, `score`:float, `price`:str, and `listing`:dict. This is given to session["search_results"] which gives the first listing to the function `suggest_outfits` in session["outfit_suggestion"]. The `session["parsed"]` item also has a branch that terminates when the parsed query in `agent.py`'s `parse_query` function is.

---

## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.
     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

```
$ python app.py ask 'striped white pants'

```
```
(.venv) EJ@MacBookPro AI201-Unit3-Project % py app.py ask "striped white pants"

  Found:    Low-Rise Cargo Pants — Khaki — $27.0 on poshmark

  Outfit:   **Outfit 1 (Y2K Streetwear):** Pair the new Low-Rise Cargo Pants (Khaki) with the white ribbed tank top (w_003), layered under the black cropped zip hoodie (w_005). Finish the look with chunky white sneakers (w_007) and the black crossbody bag (w_10).

**Outfit 2 (Edgy Grunge):** Combine the new Low-Rise Cargo Pants (Khaki) with the oversized grey crewneck sweatshirt (w_004) and black combat boots (w_008). Add the vintage black denim jacket (w_006) for extra layering.

  Fit card: Just scored these Low-Rise Cargo Pants — Khaki on Poshmark for only $27.0! Total Y2K streetwear win. Pair them with a white ribbed tank, black cropped hoodie, and chunky sneakers for the ultimate 2000s fit. ✨

2 model calls this session, 893 prompt + 198 output tokens
```
**The three tools, tested one at a time**

**search_listings**

```
$ python -c "from tools import search_listings; print(search_listings('graphic tee', max_price=30))"

```
[{'id': 'lst_002', 'score': 10.92, 'price': 'price is inclusive to 30', 'listing':{'id': 'lst_002', 'title': 'Y2K Baby Tee — Butterfly Print', 'description': 'Supercute early 2000s baby tee with butterfly graphic. Fitted crop length. Tag says medium but fits like a small.', 'category': 'tops', 'style_tags': ['y2k', 'vintage', 'graphic tee', 'cottagecore'], 'size': 'S/M', 'condition': 'excellent', 'price': 18.0, 'colors': ['white', 'pink', 'purple'], 'brand': None, 'platform': 'depop'}}, {'id': 'lst_006', 'score': 8.67, 'price': 'price is inclusive to 30', 'listing': {'id': 'lst_006', 'title': 'Graphic Tee — 2003 Tour Bootleg Style', 'description': 'Vintage-style bootleg tee with faded graphic. Slightly boxy fit. 100% cotton, soft and worn-in.', 'category': 'tops', 'style_tags': ['graphic tee', 'vintage', 'grunge', 'streetwear', 'band tee'], 'size': 'L', 'condition': 'good', 'price': 24.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}}, {'id': 'lst_033', 'score': 7.99,'price': 'price is inclusive to 30', 'listing': {'id': 'lst_033', 'title': 'Vintage Band Tee — Faded Grey', 'description': 'Faded grey band-style tee with distressedgraphic. Crew neck. Fits boxy. Well-loved but no holes or major damage.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'band tee', 'graphic tee', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 19.0, 'colors': ['grey', 'charcoal'], 'brand': None, 'platform': 'depop'}}, {'id': 'lst_015', 'score': 4.3, 'price':'price is inclusive to 30', 'listing': {'id': 'lst_015', 'title': 'Vintage GraphicHoodie — Faded Black', 'description': 'Faded black pullover hoodie with barely-visible vintage graphic on the chest. Cozy interior. Some pilling but adds to the worn-in look.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'graphic', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 26.0, 'colors': ['black', 'charcoal'], 'brand': None, 'platform': 'depop'}}, {'id': 'lst_017', 'score': 4.04, 'price': 'price is inclusive to 30', 'listing': {'id': 'lst_017', 'title': 'Mesh Long-Sleeve Top — Black', 'description': 'Sheer black mesh long-sleeve. Great for layering under a graphic tee or over a bralette. Stretchy material, fits true to size.','category': 'tops', 'style_tags': ['y2k', 'grunge', 'goth', 'layering'], 'size': 'S/M', 'condition': 'excellent', 'price': 15.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}}, {'id': 'lst_030', 'score': 3.0, 'price': 'price is inclusiveto 30', 'listing': {'id': 'lst_030', 'title': 'Vintage Knit Vest — Argyle Brown/Cream', 'description': 'Classic argyle knit vest in brown and cream. Fits medium. V-neck. Ideal for the dark academia or preppy vintage aesthetic.', 'category': 'tops','style_tags': ['vintage', 'preppy', 'knitwear', 'dark academia', 'earth tones'], 'size': 'M', 'condition': 'good', 'price': 25.0, 'colors': ['brown', 'cream', 'tan'], 'brand': None, 'platform': 'thredUp'}}, {'id': 'lst_034', 'score': 3.0, 'price': 'price is inclusive to 30', 'listing': {'id': 'lst_034', 'title': 'Bucket Hat — Reversible, Brown Plaid', 'description': 'Reversible bucket hat — plaid on one side, solid tan on the other. Unstructured brim. One size fits most.', 'category': 'accessories', 'style_tags': ['90s', 'streetwear', 'vintage', 'accessories'], 'size': 'OneSize', 'condition': 'excellent', 'price': 14.0, 'colors': ['brown', 'tan', 'plaid'], 'brand': None, 'platform': 'thredUp'}}, {'id': 'lst_038', 'score': 3.0, 'price':'price is inclusive to 30', 'listing': {'id': 'lst_038', 'title': 'Denim Vest — Medium Wash, Studded', 'description': 'Denim vest with silver stud detailing along the collar and pockets. Classic rock-inspired customization. Fits like a medium.', 'category': 'outerwear', 'style_tags': ['grunge', 'vintage', 'denim', 'customized', 'rock'], 'size': 'M', 'condition': 'good', 'price': 27.0, 'colors': ['medium blue'],'brand': None, 'platform': 'depop'}}, {'id': 'lst_011', 'score': 1.99, 'price': 'price is inclusive to 30', 'listing': {'id': 'lst_011', 'title': 'Low-Rise Cargo Pants — Khaki', 'description': 'Y2K era low-rise cargo pants. Lots of pockets. Khaki color, slightly distressed at the hems. Great for layering with a long tee.', 'category': 'bottoms', 'style_tags': ['y2k', 'cargo', '2000s', 'streetwear'], 'size': 'W29', 'condition': 'fair', 'price': 27.0, 'colors': ['khaki', 'tan'], 'brand': None, 'platform': 'poshmark'}}]

**suggest_outfit**

```
$ python -c "from tools import suggest_outfit; from utils.data_loader import get_example_wardrobe, load_listings; print(suggest_outfit(load_listings()[0], get_example_wardrobe()))"

```
**Outfit 1: Casual Streetwear**
Pair the new Vintage Levi's 501 Jeans (New Item) with the white ribbed tank top (w_003), layered under the oversized grey crewneck sweatshirt (w_004). Finish the look with chunky white sneakers (w_007) and the black crossbody bag (w_010).

**Outfit 2: Edge & Denim**
Combine the Vintage Levi's 501 Jeans (New Item) with the black cropped zip hoodie (w_005) and the vintage black denim jacket (w_006) for a double-denim moment. Ground the outfit with black combat boots (w_008) and the brown leather belt (w_009).

**create_fit_card**

```
$ python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('jeans and white sneakers', load_listings()[0]))"

```
Just scored these Vintage Levi's 501 Jeans in a sick medium wash on depop for only$38.0. The vintage denim streetwear vibe is unmatched and the fit is genuinely chefs kiss. Throw them on with some fresh white sneakers and an oversized tee for the ultimate lazy-day fit. 🤌✨

---

## How I Used AI

<!-- Two specific moments. What you asked, what came back, what you changed.

     "I used Claude to help me code" is not enough.

     "I gave Claude my search_listings spec. It returned None on no match
     instead of an empty list, so I changed it" is the level we want. -->

**Moment 1**

- *What I asked for:* I asked Gemini to help me think of how to score keywords and gave it my `search_listings` tool as descriptions and my `app.py` fields in `listings.json` as references.
- *What came back:* It suggested for me to use a industry standard Bm25 system where the listings are scored higher based on things that are rarer.
- *What I changed:* It gave a generic scoring system whose average scores were too high, so I had certain keywords get higher scores in order for the scores to not be too high. Additionally, I added a size scoring addition as well. If the item size matches exactly with `max_size` it will earn 3 full pts, while inclusive sizes get 1.5 pts, and the rest gets 0 pts. I wanted there to be more room for error on sizes so I suggest it to provide a score of  0.75pts when the size is 2 sizes smaller or larger.

**Moment 2**

- *What I asked for:* I asked Gemini to attack my acceptance criteria that I drafted.
- *What came back:* It provided edge cases and a perspective on testing indicating how there can be errors that happens such as false positives, LLM hallucinations, and empty matches in the `search_listings`, sessions getting modified after being recieved by other tools, and more. It gave me session["error"] example that helped me write errors for empty search results.
- *What I changed:* As a result I added a check to ensure the right instance is being passed, and the item searches the correct field such as the `listing` field since it has the metadata of the item inside. I asserted the objects identity as well to ensure the fields don't change after being passed to the session before being sent to the next tool `suggest_outfit`.

# Unit 4

<!-- ═══════════════════════ UNIT 4 — THE TEST ═══════════════════════

     Don't fill these in during unit 3.
     ═══════════════════════════════════════════════════════════════════ -->
## Required Features
- [x] One tool moved onto MCP. Pick one of your three. Your agent calls it through MCP from then on. The other two stay as they are.
- [x] Every failure mode triggered on purpose. Three of them, listed in Milestone 2. Each one gets a handler that tells the user what broke and what to try.
- [x] A trace of one full run. Every tool call in order, with what went in and what came back.
- [ ] Five tries per criterion. One run isn't a test. Two of your tools call a model, so the same input can give different answers. Your criteria name a target out of five, so five tries is what it takes to score one.
- [ ] A verdict and a diagnosis for each of your five criteria. Met or missed against the previous unit's target, and for every miss, which tool and which step. "It got it wrong" isn't a diagnosis.
- [ ] One improvement, made and measured. Change one thing, run the test again, put both results side by side.
## Stretch Features
**Unit 4**
- [ ] A second improvement
  - [ ] Improved query parse regex
  - [ ] Improved `tools.py::search_listings` pipeline by ordering steps. Filter, score, sort, and truncate

---

## Run Log — Before

<!-- Five criteria, five tries each, in this exact format.

     Five, because your criteria are written out of five. Mark each try PASS
     or FAIL, count the passes, and read that count against your target — a
     row targeting 4 of 5 with three PASS cells is MISSED (3/5).

     `python run_eval.py --label before` runs everything and writes the table
     into results/. Paste it here and fill in the verdicts. -->

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1. matching query completes | 4 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 2. impossible query stops early | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| empty wardrobe _(diagnostic — not one of your five)_ | None | PASS | FAIL | PASS | PASS |  PASS | (4/5) |
| 3. tool equals session metadata check | 3 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 4. unqiue fit card caption | 3 of 5 | FAIL | FAIL | FAIL | FAIL | FAIL | MISSED (0/5) |
| 5. mention wardrobe clothes in outfit | 5 of 5 | FAIL | FAIL | FAIL | FAIL | FAIL | MISSED (0/5) |

**Real output from one try**, pasted as text, naming the file and function that produced it. Has real ouput for each criterion from one try:

file: `run_2026-10-11_1644_before.md` produced by: `app.py::run_agent`

### matching query completes

- Query: `vintage graphic tee under $30`
- Wardrobe: example

**Try 1**

- stopped early: no
- selected_item: Y2K Baby Tee — Butterfly Print ($18.0, depop)
- search_results: 10

Outfit suggestion:

```
**Outfit 1: Y2K Streetwear**
Pair your new Y2K Baby Tee (New Item) with the Baggy straight-leg jeans, dark wash (lst_019), layered under the Black cropped zip hoodie (lst_023). Finish the look with the Chunky white sneakers (lst_025) and Black crossbody bag (lst_028).

**Outfit 2: Casual Grunge**
Combine the Y2K Baby Tee (New Item) with the Wide-leg khaki trousers (lst_020) secured by the Brown leather belt (lst_027). Throw on the Vintage black denim jacket (lst_024) and step into the Black combat boots (lst_026) to complete the vibe.
```

Fit card:

```
Scored this Y2K Baby Tee — Butterfly Print on depop for just $18.0 in pristine condition. 🦋 I'm styling it with baggy jeans and a zip hoodie for the ultimate Y2K streetwear fit. Such a good vintage graphic find!
```

Trace:

```
[1] parse_query
      in:  dict with keys: query
      out: dict with keys: description, size, max_price
[2] search_listings
      in:  dict with keys: query, size_filter, max_price
      out: Found 10 items
[3] select_item
      in:  dict with keys: results_count
      out: Y2K Baby Tee — Butterfly Print ($18.0, S/M, depop)
[4] suggest_outfit
      in:  dict with keys: item_id
      out: **Outfit 1: Y2K Streetwear** Pair your new Y2K Bab...
[5] create_fit_card
      in:  dict with keys: item_id
      out: Scored this Y2K Baby Tee — Butterfly Print on depo...
```

### impossible query stops early

- Query: `designer ballgown size XXS under $5`
- Wardrobe: example

**Try 1**

- stopped early: yes — No items matched your search for 'designer ballgown' in size XXS under $5.00. Try loosening your price cap, broadening your search terms, or checking for other sizes.
- selected_item: (none)
- search_results: 0

Trace:

```
[1] parse_query
      in:  dict with keys: query
      out: dict with keys: description, size, max_price
[2] search_listings
      in:  dict with keys: query, size_filter, max_price
      out: Found 0 items
```

### empty wardrobe

- Query: `denim jacket under $50`
- Wardrobe: empty

**Try 1**

- stopped early: no
- selected_item: Denim Jacket — Light Wash, Cropped ($42.0, poshmark)
- search_results: 10

Outfit suggestion:

```
Pair this light wash cropped denim jacket (w_001) with high-waisted black wide-leg trousers (non-wardrobe) and a fitted white crop top (non-wardrobe) for an effortless streetwear contrast. Finish with chunky retro sneakers (non-wardrobe) and a minimalist black shoulder bag (non-wardrobe). 

Alternatively, layer the jacket (w_001) over a floral midi slip dress (non-wardrobe) to lean into a vintage, textured aesthetic. Complete this look with strappy leather sandals (non-wardrobe) and delicate silver hoop earrings (non-wardrobe).
```

Fit card:

```
Just scored this vintage Denim Jacket — Light Wash, Cropped for $42.0 on Poshmark! Throw it on with high-waisted black wide-leg trousers and a fitted white crop top for the ultimate streetwear contrast. 🤌✨
```

Trace:

```
[1] parse_query
      in:  dict with keys: query
      out: dict with keys: description, size, max_price
[2] search_listings
      in:  dict with keys: query, size_filter, max_price
      out: Found 10 items
[3] select_item
      in:  dict with keys: results_count
      out: Denim Jacket — Light Wash, Cropped ($42.0, S, poshmark)
[4] suggest_outfit
      in:  dict with keys: item_id
      out: Pair this light wash cropped denim jacket (w_001) ...
[5] create_fit_card
      in:  dict with keys: item_id
      out: Just scored this vintage Denim Jacket — Light Wash...
```

### tool equals session metadata check

- Query: `white polo golf shirt under $20`
- Wardrobe: example

**Try 1**

- stopped early: no
- selected_item: Vintage Polo Shirt — Forest Green ($18.0, thredUp)
- search_results: 10

Outfit suggestion:

```
**Outfit 1: Streetwear Prep**
Pair the Vintage Polo Shirt (NEW ITEM) with the baggy straight-leg jeans (lst_019), brown leather belt (lst_027), and chunky white sneakers (lst_025). Layer the vintage black denim jacket (lst_024) on top for a sharp, textured contrast. 

**Outfit 2: Minimal Earth Tones**
Tuck the Vintage Polo Shirt (NEW ITEM) into the wide-leg khaki trousers (lst_020) secured with the brown leather belt (lst_027), and finish with the black combat boots (lst_026) and black crossbody bag (lst_028) for an effortless, grounded look.
```

Fit card:

```
Just snagged this Vintage Polo Shirt in forest green for only $18.0 on thredUp and the preppy earth-tone vibe is unmatched. 

Throw it on with some baggy denim and a black jacket for an easy streetwear look, or tuck it into wide-leg trousers with chunky boots. 🌿✨
```

Trace:

```
[1] parse_query
      in:  dict with keys: query
      out: dict with keys: description, size, max_price
[2] search_listings
      in:  dict with keys: query, size_filter, max_price
      out: Found 10 items
[3] select_item
      in:  dict with keys: results_count
      out: Vintage Polo Shirt — Forest Green ($18.0, M, thredUp)
[4] suggest_outfit
      in:  dict with keys: item_id
      out: **Outfit 1: Streetwear Prep** Pair the Vintage Pol...
[5] create_fit_card
      in:  dict with keys: item_id
      out: Just snagged this Vintage Polo Shirt in forest gre...
```

### unqiue fit card caption

- Query: `90s track jacket in size M`
- Wardrobe: example

**Try 1**

- stopped early: yes — No items matched your search for '90s track jacket in' in size M. Try loosening your price cap, broadening your search terms, or checking for other sizes.
- selected_item: (none)
- search_results: 0

Trace:

```
[1] parse_query
      in:  dict with keys: query
      out: dict with keys: description, size, max_price
[2] search_listings
      in:  dict with keys: query, size_filter, max_price
      out: Found 0 items
```

### mention wardrobe clothes in outfit

- Query: `silk slip dress in midi length under $40`
- Wardrobe: example

**Try 1**

- stopped early: no
- selected_item: 90s Silk Slip Dress — Floral, Midi Length ($30.0, depop)
- search_results: 10

Outfit suggestion:

```
**Outfit 1: 90s Grunge**
Layer the new 90s Floral Slip Dress (new item) over the White ribbed tank top (w_003), and pair with the Black combat boots (w_008) and Vintage black denim jacket (w_006). Complete the look using the Black crossbody bag (w_010).

**Outfit 2: Streetwear Contrast**
Throw the Oversized grey crewneck sweatshirt (w_004) directly over the 90s Floral Slip Dress (new item) to wear it as a skirt, and finish with the Chunky white sneakers (w_007) and Black crossbody bag (w_010).
```

Fit card:

```
Just snagged this 90s Silk Slip Dress — Floral, Midi Length on depop for literally $30.0 and I'm obsessed. Style it 90s grunge over a ribbed tank with combat boots, or throw an oversized crewneck right over it to wear it as a skirt. 🌱✨
```

Trace:

```
[1] parse_query
      in:  dict with keys: query
      out: dict with keys: description, size, max_price
[2] search_listings
      in:  dict with keys: query, size_filter, max_price
      out: Found 10 items
[3] select_item
      in:  dict with keys: results_count
      out: 90s Silk Slip Dress — Floral, Midi Length ($30.0, M, depop)
[4] suggest_outfit
      in:  dict with keys: item_id
      out: **Outfit 1: 90s Grunge** Layer the new 90s Floral ...
[5] create_fit_card
      in:  dict with keys: item_id
      out: Just snagged this 90s Silk Slip Dress — Floral, Mi...
```

---

## Verdicts and Diagnoses

<!-- MET or MISSED per criterion against LAST UNIT's target, plus a sentence on
     how you decided.

     Then, for every miss: which of the four places it happened — a tool, the
     loop's branch, the session, or the model's output — AND the mechanism.

     Not a diagnosis:  "The fit card was bad."
     A diagnosis:      "The fit card criterion missed on 2 of 5 items. Both had
                        an empty brand field. My prompt puts the brand in the
                        first sentence, so the card opened with a blank and read
                        like a fragment. The tool worked; the prompt assumed a
                        field that isn't always there."

     Look for a pattern. Three misses on the same tool is one problem, not
     three. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | matching query completes | 4 of 5 | MET | Each try did not miss the criteria at all. The agent completely goes through all three tools: `search_listings`, `suggest_outfit`, and `create_fit_card` which also matches a listing and returns a outfit card. |
| 2 | impossible query stops early | 5 of 5 | MET | Each try with the impossible query stops. None give `suggest_outfits` a listing and the agent stops. The `selected_item` is always (none) and `search_results` is 0 in all 5 tries. |
| 3 | tool equals session metadata check | 3 of 5 | MET | Each try meets the criteria. In `agent.py::run_agent` the agent saves the session metadata before passing and after passing to `suggest_outfits` to compare them which would fail if it didn't equal the same. The `out` also ends up being the same in the trace step for `select_item` and for `suggest_outfit`. |
| 4 | unqiue fit card caption | 3 of 5 | MISSED | Every try failed the criteria so it didn't meet even the minimum number of passes. This is because the search stopped early due to no items matching search. <br/>**Step failed:** `agent.py::search_listings` tool <br/>**Mechanism it failed in:** inside a helper tool `parse_query`. <br/><br/> It doesn't properly validate strings it can and it cannot find in listings before giving the strings to `search_listings`. The criteria didn't have the chance to be evaluated because it never reached the step for `agent.py::create_fit_card`. |
| 5 | mention wardrobe clothes in outfit | 5 of 5 | MISSED | Every try failed the criteria so it didn't meet even the minimum number of passes. <br/>**Step failed:** `agent.py::suggest_outfits` tool <br/>**Mechanism it failed in:** inside the AI `prompt` instructions where the response was made and generated. <br/><br/> The criteria failed silently. Wardrobe items need to be explicitly identified which didn't happen for any of these tries. This is strange since in the empty wardrobe disagnostic it indicated which items were not in the wardrobe, opposite of what it was supposed to do. <br/> <br/> Criteria 1 - try 5 and the 5 diagnostic runs (empty wardrobe) and only the 1 of 25 tries in criteria 1 had some form of information about the wardrobe being empty or the clothes coming from the wardrobe. <br/> <br/>This error can come from information or instructions missing from the generated prompt indicating the model can be overloaded and be skipping over instructions.<br/> <br/> Possible solutions may be that `suggest_outfits` may need to lower what it asks the model to do and generate. By asking for only item ids to be generated then returned we reduce the risk of AI hallucinations. By checking what is in the wardrobe ourselves and what to print we can improve the answer to meet the criteria. Additionally, adding more instructions may also add more priority to explicitly mentioning if the items are from the wardrobe but would probably contribute to more AI hallucinations. |

**Diagnoses**

We cannot test a tool if we cannot even get to it. The foundation of this agent is `search_listings`. The way the asked or searched query is handled, split, parsed, and distributed matters before anything else.

If the algorithm and step by step process of finding listings is bad then the AI model cannot hope to be accurate or find a valid listing.

Additionally, having an AI model do all of the work isn't always the correct solution either. In `suggest_outfits` AI model generation should be smaller compared to `create_fit_card`. The AI implementation for the `suggest_outfits` tool increases the risk of hallucination. Having minimal AI usage and more code solutions allows the tool to produce accurate results.


---

## Loop Trace
[ **For each loop trace below will not have trace steps unless it is required since there is a unintended bug.** ]

In `trace.py::step` "**flush=True**" which by default is a unintended bug which prints `trace.step` even when the `--trace` argument is not true. 

```print(line, flush=True)``` needs to be in a conditional to not print traces by default.
```python
if ENABLED:
     print(line, flush=True)
```


<!-- One full run, printed step by step, with the MCP call visible in it.

     `python app.py ask '...' --trace` once you've added the trace.step()
     calls in Milestone 2.

     Worth pasting BOTH the happy path and the empty-search path. The empty
     one should be visibly shorter, because it stops. If your two traces are
     the same length, your branch isn't working — and this is the fastest way
     anyone will ever find that out. -->

**Happy path** Sucessful query and result

✅ A result that works

```zsh
python app.py ask 'vintage tee'
  Found:    Vintage Band Tee — Faded Grey — $19.0 on depop

  Outfit:   **Outfit 1: Ultimate Grunge**
Pair your new Vintage Band Tee (new item) with the baggy dark wash straight-leg jeans (lst_01, bottoms), black combat boots (lst_08, shoes), and layer the vintageblack denim jacket (lst_06, outerwear) on top. Complete the look with the black crossbody bag (lst_10, accessories). 

**Outfit 2: Streetwear Contrast**
Tuck your new Vintage Band Tee (new item) into the wide-leg khaki trousers (lst_02, bottoms) cinched with the brown leather belt (lst_09, accessories), and finishwith chunky white sneakers (lst_07, shoes) and the black cropped zip hoodie (lst_05, tops) worn unzipped.

  Fit card: Just scored this Vintage Band Tee for only $19.0 on depop and the fade is seriously unmatched. Throw it on with baggy dark wash jeans and chunky combat boots for the ultimate grunge fit. 🤘✨

2 model calls this session, 905 prompt + 210 output tokens
```

**Empty search.** Failed silently. Should've returned an empty list showing no matches for real lightsaber 

❌ Query something my data cannot possibly have.

```zsh
py app.py ask 'real lightsaber under $30'
  Outfit:   **Outfit 1: 90s Streetwear**
Pair the new bucket hat (plaid side) with the white ribbed tank top (w_003), baggy straight-leg jeans (w_001), vintage black denim jacket (w_006), and chunky white sneakers (w_007). Add the black crossbody bag (w_010) to finish the look.
      
**Outfit 2: Casual Earth Tones**
Wear the new bucket hat (solid tan side) over the oversized grey crewneck sweatshirt (w_004) tucked into the wide-leg khaki trousers (w_002), secured with the brown leather belt (w_009). Ground the fit with the black combat boots (w_008).

  Fit card: Found this 90s streetwear gem on thredUp for just $14.0! I'm totally obsessed with the Bucket Hat — Reversible, Brown Plaid for finishing off baggy denim fits. 🤎

2 model calls this session, 906 prompt + 211 output tokens

```

**Empty wardrobe.** Failed sucessfully. 

✅ It should return advice, not a crash and not an empty string

```zsh
py app.py ask '...' --empty-wardrobe
(running with an empty wardrobe)

  No items matched your search for '...'. Try loosening your price cap, broadening your search terms, or checking for other sizes.

0 model calls this session
```

**Model unavailable** Failed successfully

✅ The agent should say the model couldn't be reached — not hang, and not show a raw stack trace. 

```
py app.py ask 'grey white beanie under $30'
  Model service unavailable: The model rejected your API key. Check GEMINI_API_KEY in your .env file, or create a fresh key at aistudio.google.com.

1 model calls this session
```

**On the MCP move:** 
The rewire for `search_listings` to `mcp_server.py` worked and went through. There were problems with print lines though if tools.py printed anything but after removing them and it was resolved after ensuring there were no string buffers lingering inside the stream because of either `agent.py` and `tools.py`. The error was caused by `parse_query::agent.py` which did not have good regular expressions and did not have conditional checks on whether the group() function would work in the code. Additionally, `mcp_server.py` got handlers
```
MCPError: Couldn't call 'search_listings' over MCP: unhandled errors in a TaskGroup (1 sub-exception)

Check that mcp_server.py runs on its own first:

    python mcp_server.py

If it exits immediately with an error, fix that before coming back here. 
```
<!-- what changed in your code, and whether anything
behaved differently afterwards. If the rewire didn't work, say exactly where it
broke — the error text and the last thing that worked. That earns the point in
full. -->

---

## The Improvement

<!-- What you changed, why your diagnosis pointed at it, and the after-run in
     the same table format. One change, measured properly.

     `python run_eval.py --label after` -->

**What I changed:**

**Which failure it was meant to fix:**

### Run Log — After

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |  |  |
| 2.  |  |  |  |  |  |  |  |
| 3.  |  |  |  |  |  |  |  |
| 4.  |  |  |  |  |  |  |  |
| 5.  |  |  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it made things worse, say that. Honestly reported, that earns full
     credit and is more interesting than one that worked. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped where
     you did. "I ran out of time" is fine if it's true. Pretending nothing is
     left is not. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 3

       [ ] criteria.md has five numbered criteria, each with a target
       [ ] Each criterion has a reason underneath it
       [ ] All five unit 3 sections above have real content
       [ ] Tool Inventory: all three tools, inputs WITH TYPES, a specific
           return value, and the empty case
       [ ] Planning Loop names the branch rule and agent.py::run_agent
       [ ] Sample Run: one full query plus the three per-tool tests, as text
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN, you submit the same one
           next unit

     SUBMISSION CHECKLIST — unit 4

       [ ] mcp_server.py exists with one tool registered
           (or a written record of exactly where the rewire broke)
       [ ] Run Log — Before, five criteria, five tries each
       [ ] Real output pasted underneath, naming file and function
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, naming a place AND a mechanism
       [ ] Loop Trace, with the MCP call visible in it
       [ ] All three failure modes triggered and handled
       [ ] One improvement, with Run Log — After in the same format
       [ ] What's Still Broken
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository. Your commit history is what
     shows your criteria existed before your results did.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
