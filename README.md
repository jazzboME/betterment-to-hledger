# betterment-to-hledger

This repository contains a bash script to convert betterment activity report PDFs into hledger entries. 

If you try this, obviously the conversion may not be perfect and you may want to hand touch up any issues, such as the accounts from where your deposits originate. 

The script takes an opionated view of things and posts commodity transactions at cost (@@) as well as using the cost to derive a more accurate share quanity to six places.

So expect a statement that reads:
```
FUND	   Price     Shares    Value
STOCK      $51.90    -0.111    -$5.78
```
to output as
```
assets:investing:betterment:STOCK  -0.111368 STOCK @@ $5.78 ; 0.111 priced at $51.90
```
**Note:** This script was tested on Betterment statements from approximately 2014 to late 2022, so it may not support activity reports dated after that.

## Pre-Requisites

This was developed on Mac OS X and uses the built in (BSD derived) commands for awk, grep and sed.

Additionally it relies on perl being installed and `pdftotext` from Poppler. 

(I used homebrew to install both bash and poppler.)

## Additional Pre-Configuration

You'll likely want to change the values for the following variables:
`transBase`: the root of your Betterment accounts in hledger
`transFees` : the account where fees should be changed

Then below that you will specify any additional subaccounts to differentiate your betterment accounts that will be specified at run-time.

By default deposits will originate from `equity:start`, withdrawals will go to `assets:bank` and fees will be charged to `expenses:betterment:fees`

## Installation and Running

You'll want to copy your (configured) copy of `bstate` into your local path.

For each betterment statement you'll want to pass both the path to the PDF containing the activity report and the betterment account short hand you want to post the transactions to.

The script will put informational, debugging messages and errors in STDERR, while the actual hledger transactions will go to STDOUT for redirection.

Typically the script will be run this way:
```
for f in *.pdf; do
    bstate "$f" wealth >> betterment-wealth.journal
done
```

This would process all of the pdf files in the current directory, and applying the configured rules for transactions for the user's "wealth" account. You will see it process each file (and indicate any errors) while pushing the output into a `.journal` file.

Populating the environment variable `BSTATE_DEBUG` will add debugging output to STDERR.


