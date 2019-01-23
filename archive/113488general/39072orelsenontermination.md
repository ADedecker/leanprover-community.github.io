---
layout: page
title: Lean Prover Zulip Chat Archive 
permalink: archive/113488general/39072orelsenontermination.html
---

## Stream: [general](index.html)
### Topic: [orelse nontermination](39072orelsenontermination.html)

---

#### [Seul Baek (Dec 26 2018 at 06:58)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/orelse%20nontermination/near/152538868):
```
meta def foo : tactic unit :=
triv <|> foo

example : true := by foo
```
In this example I expected `foo` to behave just like `triv` since the right branch would never be used, but it times out. Is there a way to avoid this?

#### [Mario Carneiro (Dec 26 2018 at 07:58)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/orelse%20nontermination/near/152540454):
you can eta expand `foo` on the right

#### [Gabriel Ebner (Dec 26 2018 at 09:19)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/orelse%20nontermination/near/152542912):
Another nice option is to use `local attribute [inline] interaction_monad_orelse`.

#### [Seul Baek (Dec 26 2018 at 13:15)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/orelse%20nontermination/near/152550157):
@**Mario Carneiro**  I thought you meant changing the body of `foo` to `triv <|> (λ s, foo s)`, but this still times out. Am I doing something wrong here?

#### [Seul Baek (Dec 26 2018 at 13:17)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/orelse%20nontermination/near/152550222):
@**Gabriel Ebner**  That works. Thanks!

#### [Mario Carneiro (Dec 26 2018 at 21:07)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/orelse%20nontermination/near/152569135):
actually I think you have to eta expand the definition of foo itself, i.e. `def foo | s := (triv <|> foo) s`

