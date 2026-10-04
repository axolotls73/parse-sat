# ParseSAT

The world's worst SAT solver!

`sat.grammar` is a context-sensitive grammar that recognizes satisfiable boolean formulas containing ∨, ∧, and ¬.
It has 171 rules, 26 nonterminals, and 6 terminals, and was pretty painful to write.

Since the grammar recognizes SAT formulas, all you need to do to make a solver is convert your formula to a valid output string (see below for an example) and then parse the string with a CSG parser!
This is _extremely_ slow from the few tests I've run.
I'll add a (hopefully better) parser eventually, but figuring out `(xx ∧ ¬(xx))` was unsat took 6 seconds with a Python version.

Some restrictions:
- Variable names are of the form `xx`, `xxx`, `xxxx`, etc. I want to add binary names at some point, but this was the easiest to implement
- Yes the variable names have to be of length >= 2, this is a hack to get around CSG limitations (no erasing characters)
- Every formula has to be surrounded by (at least) two sets of parentheses, this is another hack so I could add start and end nonterminals (as opposed to marking every terminal and nonterminal since I can't erase characters) and thus fewer rules

Here's an example derivation for the formula `a ∨ b`:

```
S
(( $ Ft E
(( $ (Ft ∨ Fa) E
(( $ (α V ∨ Fa) E
(( $ (α V ∨ φ V) E
(( $ (α Xu ∨ φ V) E
(( $ (α Xu ∨ φ Xu V) E
(( $ (α Xu ∨ φ Xu Xu) E
(( ( $ α Xu ∨ φ Xu Xu) E
(( ( $ α XLcα ∨ φ Xu Xu) E
(( ( $ α ∨ XLα φ Xu Xu) E
(( ( $ α ∨ φ Xm XLαs Xu) E
(( ( $ α ∨ φ Xm Xm XLαs) E
(( ( $ α ∨ φ Xm Xm) XLα E
(( ( $ α ∨ φ Xm Xm) XLD E
(( ( $ α ∨ φ Xm Xm XLD) E
(( ( $ α ∨ φ Xm XLD Xu) E
(( ( $ α ∨ φ XLD Xu Xu) E
(( ( $ α ∨ XLD φ Xu Xu) E
(( ( $ α XLD ∨ φ Xu Xu) E
(( ( $ XLD x ∨ φ Xu Xu) E
(( ( $ x x ∨ φ Xu Xu) E
(( ( x $ x ∨ φ Xu Xu) E
(( ( x x $ ∨ φ Xu Xu) E
(( ( x x ∨ $ φ Xu Xu) E
(( ( x x ∨ x $s Xu Xu) E
(( ( x x ∨ x x $s Xu) E
(( ( x x ∨ x x x $s) E
(( ( x x ∨ x x x) $ E
(( ( xx ∨ xxx ) ))
```

To make it easy to write the grammar, I used Lean bindings for Unicode characters.
I had Claude make a VSCode extension to add syntax highlighting for .grammar files (in `vscode-csg/`), and put this in my `settings.json` (this requires having the Lean 4 extension installed already):
```
"lean4.input.languages": ["lean4", "lean", "csg"]
```

TODO: add a DIMACS frontend + CSG parser implementation so this is runnable!
