---
name: lotgen-assets
description: >
  What a LOTGEN lot is ABOUT, and getting files in and out — reading a product page into
  a brief, saving products and their photography, building a brand kit from a site, and
  moving files between the workspace and Google Drive. Use when the user says "here is
  our product page", "use our branding", "import from Drive", "send it to Drive", "save
  this product", "get our logo", or asks why a Drive import found nothing. Nothing here
  renders or spends.
---

# Assets

Everything in this file is **free**. Reading a page, saving a product, building
a brand kit, importing and exporting all cost nothing — they move and record
material rather than generating it.

---

## READING A PAGE: DETERMINISTIC, AND HONEST WHEN THIN

`extract_product` reads a public product page into a brief — name, summary,
selling points, price, photos. `create_brand_kit_from_url` does the same for a
brand: the mark, the one colour the site states about itself, the brand name.

**Neither calls a model.** They read schema.org, OpenGraph and meta tags. That
is the important property: **a page carrying nothing structured returns a thin
brief rather than invented copy.** What came back names its own sources, so
read that rather than assuming a full brief.

Two failures to expect and handle rather than retry:

- **Many large storefronts block automated readers.** When that happens, do not
  keep trying and do not fabricate. Write the brief yourself with the user, and
  pass it directly to the lot builder.
- **A logo that will not download** is named, and the kit is still made. That
  is not a failed call.

A thin result is information. Tell the user the page gave up little and offer
to write it together; do not paper over it with plausible marketing copy,
which is exactly what these tools were built to avoid.

---

## SAVING, SO THE NEXT AD STARTS FROM SOMETHING

```
extract_product   read the page          → a brief
save_product      keep it                → later ads start here, not blank
add_product_images attach the photography → downloaded ONCE into the workspace
```

**Attached images are downloaded once and then reused by reference.** So an ad
still builds after the source page has gone — which is the whole reason to
attach them rather than pass URLs each time. **URLs that fail are named, never
swallowed**; read that list rather than assuming everything landed.

`list_products` and `get_product` find what is already there. Check before
extracting a page again.

---

## FILES ON THIS MACHINE, AND WHAT EACH PICTURE IS

A URL goes through `add_product_images`. A file that exists only on the user's
disk does not, and **never write a file into a tool call as base64**: one photo
is hundreds of thousands of tokens.

```
upload_product_assets   one link per file    → a curl line for each
(run each curl line)    the shell posts it   → the asset it became, tagged
set_product_asset_tags  say what each one is → per asset, tagged and failed
```

`upload_product_assets` takes each file's path where your shell runs and the
tag it should land with, and answers with one single-use link per file and a
curl line that sends it. Run the lines as written: the bytes go from the shell
to LOTGEN and never through the conversation. Each link takes one file and
lasts fifteen minutes. A file refused for its type or size does not spend the
link, so fix the file and run the same line again. A packshot or a product map
has to be a still, and a clip named as one is refused before any link exists.

**No shell, no upload.** A chat client with no code execution, or a sandbox
that cannot reach the API host, cannot send a file through this tool. The
honest routes are then a public URL (`add_product_images`), Drive
(`import_from_drive`, below), or the person dropping the file on the product
in the web app. Say which, and do not report that it landed.

**Tag what you add.** The tag says what the picture IS: packshot, product map,
lifestyle, how it works, infographic, packaging, beauty shot, product in
packaging. It decides what a model sees. The pack sends the product map first,
then packshots, then the rest, so a model that takes one reference gets the
product map whenever the product has one, and the prompt names each picture by
its tag. `set_product_asset_tags` tags several at once from the ids
`get_product` lists, and a null `role` clears one. A logo is not product
photography; it belongs on a brand kit. Lots already built keep the tags they
were built with.

**A brand kit is a set of DEFAULTS for the lots beneath it.** Tone is one
sentence of voice; colours are ordered hex and become a palette clause on the
lot's look rather than raw tokens dropped into a prompt; the default look
applies to any lot that declares none. Build one with `save_brand_kit` by hand,
or `create_brand_kit_from_url` from the site. `list_brand_kits` to see what
exists.

---

## DRIVE IN: YOU CANNOT GET THE FILE IDS

This is the one that wastes the most time if you do not know it.

**No tool will give you Drive file ids, and there is deliberately no browse or
list tool.** Under the scope this app holds, it can only read files a person
picked in Google's own Picker, in the web app — **the picking IS the permission
grant.**

So the procedure is:

1. Ask the person to open **Import from Drive** beside the upload control they
   want — product photos, a creator, a shot's references, its opening frame,
   its voice source.
2. Use the ids they hand you, or simply let them complete the import there.

**An id you invented or guessed reaches nothing.** Do not try to construct one
from a Drive URL the user pasted, and do not ask the model to recall one.

`import_from_drive`'s destination decides both where the file lands and what is
allowed — one destination takes stills and clips, one takes a still only and
lands it as a **reusable workspace-level face** rather than a one-lot
reference, one takes audio formats. A Google Doc, Sheet or Slides **has no file
bytes** and is refused by name; that is not a bug to retry.

**Results come back per file, never as a count.** Imported and failed arrive
together, so a batch where one file was the wrong type still imports the rest.
Report which one did not arrive and why. A file marked as reused was already
here and nothing was downloaded again.

---

## DRIVE OUT: IT RETURNS BEFORE IT LANDS

`export_to_drive` **never re-renders and never spends** — it uploads takes that
already exist.

It answers immediately with an export to poll. The files move on our server and
can take minutes, so **the call returning is not the export finishing.** Poll
`get_drive_export` until it settles.

- The default sends **one file per shot**, the mounted take, named the way the
  zip names it. The other mode gives each shot a folder with its mounted take
  beside the rest.
- **A shot with nothing mounted is named in the manifest rather than silently
  skipped.** Read that list — it is usually the real answer to "why is the
  delivery short".
- **Partial is not failure.** It means the rest of the lot did land. Report
  which files are missing, not the whole export as broken.
- A second call while one is running returns the **running** export rather than
  sending the lot twice.

**Four different refusals, and only two are about the connection.** Two mean
there is no Drive grant or it has expired — only a person can fix that, in
Settings → Workspace, so say so and stop rather than retrying. The other two
are about the **destination**: the delivery target is gone, or this Google
account can open that shared drive but not write to it. Both are fixed in the
same tab by choosing another. Telling a user to reconnect when the real problem
is a read-only shared drive sends them round a loop that cannot help.

---

## ONE TRAP WITH ASSET URLS

`get_asset_url` gives a signed, time-limited URL for one asset — useful for
showing somebody a file.

**It is not how you look at a picture yourself.** The URL is on the storage
host, and an agent in a sandbox usually cannot open it. `view_take` returns
the picture in the result instead: `job_id` for a take, `asset_id` for a
reference, a product photo or a last frame.

**Do not use it to attach a still to another lot.** Pass the **asset id**
instead, to the image field or to `create_ref_pack`. Locally the signed URL is
a loopback address and will not fetch, so an attachment built from one works on
your machine and fails everywhere else. `list_assets` is where the ids are.

---

## CHECKLIST

1. Did the page actually give up anything, or is this a thin brief you should
   say is thin?
2. Are the product's images **attached**, so the ad survives the page going
   away?
3. For a Drive import: has a person picked the files? You cannot obtain ids
   any other way.
4. Did you read the per-file results rather than assuming the batch worked?
5. For a Drive export: did you poll to completion, and read the missing-shots
   list?
6. Is the refusal about the connection or the destination? They have different
   fixes.
7. Attaching an asset: id, never the signed URL.
