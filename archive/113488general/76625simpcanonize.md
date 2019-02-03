---
layout: page
title: Lean Prover Zulip Chat Archive 
permalink: archive/113488general/76625simpcanonize.html
---

## Stream: [general](https://leanprover-community.github.io/archive/113488general/index.html)
### Topic: [simp canonize](https://leanprover-community.github.io/archive/113488general/76625simpcanonize.html)

---

<base href="https://leanprover.zulipchat.com">
{% raw %}
#### [ Simon Hudon (Jan 22 2019 at 18:28)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/simp%20canonize/near/156622369):
<p>I'm trying to get rid of some simp loops and I just realized that there is a part of it I don't understand and it seems to be getting into a loop. Here is the trace that Lean prints for it:</p>
<div class="codehilite"><pre><span></span>[simplify.canonize]
category_theory.types
==&gt;
category_theory.types
[simplify.canonize]
category_theory.types
==&gt;
category_theory.types
[simplify.canonize]
_inst_2
==&gt;
_inst_2
[simplify.canonize]
category_theory.types
==&gt;
category_theory.types
[simplify.canonize]
category_theory.types
==&gt;
category_theory.types
[simplify.canonize]
_inst_2
==&gt;
_inst_2
</pre></div>


<p>How do I prevent it from looping? I tried <code>simp [-category_theory.types]</code> and it doesn't work.</p>

#### [ Johan Commelin (Jan 22 2019 at 18:36)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/simp%20canonize/near/156622930):
<p>But <code>category_theory.types</code> isn't even marked as <code>[simp]</code>. How come the simplifier is using it?</p>

#### [ Simon Hudon (Jan 22 2019 at 19:36)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/simp%20canonize/near/156627682):
<p>I am puzzled too. My tentative answer is that it comes from the <code>canonize</code> phase of <code>simp</code> which I did not know of. It seems like a phase where class instances are reduced but I can't say more. <span class="user-mention" data-user-id="110024">@Sebastian Ullrich</span>, would you care to enlighten us?</p>

#### [ Simon Hudon (Jan 22 2019 at 21:49)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/simp%20canonize/near/156638222):
<p>Update: using <code>set_option trace.debug.dsimplify true</code> and using <code>dsimp</code> instead of <code>simp</code>, I managed to find the offender which was unrelated to <code>canonize</code></p>

#### [ Mario Carneiro (Jan 22 2019 at 23:01)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/simp%20canonize/near/156643964):
<p>MWE</p>

#### [ Simon Hudon (Jan 23 2019 at 00:21)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/simp%20canonize/near/156649250):
<div class="codehilite"><pre><span></span><span class="kn">import</span> <span class="n">category_theory</span><span class="bp">.</span><span class="n">category</span>
<span class="kn">import</span> <span class="n">category_theory</span><span class="bp">.</span><span class="n">types</span>

<span class="n">universes</span> <span class="n">u</span>

<span class="n">class</span> <span class="n">comonad</span> <span class="o">(</span><span class="n">w</span> <span class="o">:</span> <span class="kt">Type</span> <span class="n">u</span> <span class="bp">→</span> <span class="kt">Type</span> <span class="n">u</span><span class="o">)</span>
<span class="kn">extends</span> <span class="n">functor</span> <span class="n">w</span> <span class="o">:=</span>
<span class="o">(</span><span class="n">extract</span> <span class="o">:</span> <span class="bp">Π</span> <span class="o">{</span><span class="n">α</span><span class="o">},</span> <span class="n">w</span> <span class="n">α</span> <span class="bp">→</span> <span class="n">α</span><span class="o">)</span>
<span class="o">(</span><span class="n">extend</span> <span class="o">:</span> <span class="bp">Π</span> <span class="o">{</span><span class="n">α</span> <span class="n">β</span><span class="o">},</span> <span class="o">(</span><span class="n">w</span> <span class="n">α</span> <span class="bp">→</span> <span class="n">β</span><span class="o">)</span> <span class="bp">→</span> <span class="n">w</span> <span class="n">α</span> <span class="bp">→</span> <span class="n">w</span> <span class="n">β</span><span class="o">)</span>

<span class="n">def</span> <span class="n">Cokleisli</span> <span class="o">(</span><span class="n">m</span><span class="o">)</span> <span class="o">[</span><span class="n">comonad</span><span class="bp">.</span><span class="o">{</span><span class="n">u</span><span class="o">}</span> <span class="n">m</span><span class="o">]</span> <span class="o">:=</span> <span class="kt">Type</span> <span class="n">u</span>

<span class="kn">open</span> <span class="n">category_theory</span> <span class="n">comonad</span>
<span class="kn">instance</span> <span class="o">{</span><span class="n">m</span><span class="o">}</span> <span class="o">[</span><span class="n">comonad</span><span class="bp">.</span><span class="o">{</span><span class="n">u</span><span class="o">}</span> <span class="n">m</span><span class="o">]</span> <span class="o">:</span> <span class="n">category</span><span class="bp">.</span><span class="o">{</span><span class="n">u</span><span class="o">}</span> <span class="o">(</span><span class="n">Cokleisli</span> <span class="n">m</span><span class="o">)</span> <span class="o">:=</span> <span class="n">sorry</span>

<span class="n">def</span> <span class="n">copipe</span> <span class="o">{</span><span class="n">w</span> <span class="n">α</span> <span class="n">β</span> <span class="n">γ</span><span class="o">}</span> <span class="o">[</span><span class="n">comonad</span> <span class="n">w</span><span class="o">]</span> <span class="o">(</span><span class="n">f</span> <span class="o">:</span> <span class="n">w</span> <span class="n">α</span> <span class="bp">→</span> <span class="n">β</span><span class="o">)</span> <span class="o">(</span><span class="n">g</span> <span class="o">:</span> <span class="n">w</span> <span class="n">β</span> <span class="bp">→</span> <span class="n">γ</span><span class="o">)</span> <span class="o">:</span> <span class="o">(</span><span class="n">w</span> <span class="n">α</span> <span class="bp">→</span> <span class="n">γ</span><span class="o">)</span> <span class="o">:=</span>
<span class="n">g</span> <span class="err">∘</span> <span class="n">extend</span> <span class="n">f</span>

<span class="kn">infix</span> <span class="bp">`</span> <span class="bp">=&gt;=</span> <span class="bp">`</span><span class="o">:</span><span class="mi">55</span> <span class="o">:=</span> <span class="n">copipe</span>

<span class="kn">instance</span> <span class="n">Cokleisli</span><span class="bp">.</span><span class="n">category</span> <span class="o">{</span><span class="n">m</span><span class="o">}</span> <span class="o">[</span><span class="n">comonad</span> <span class="n">m</span><span class="o">]</span> <span class="o">:</span> <span class="n">category</span> <span class="o">(</span><span class="n">Cokleisli</span><span class="bp">.</span><span class="o">{</span><span class="n">u</span><span class="o">}</span> <span class="n">m</span><span class="o">)</span> <span class="o">:=</span>
<span class="o">{</span> <span class="n">hom</span> <span class="o">:=</span> <span class="bp">λ</span> <span class="n">α</span> <span class="n">β</span><span class="o">,</span> <span class="n">m</span> <span class="n">α</span> <span class="bp">→</span> <span class="n">β</span><span class="o">,</span>
  <span class="n">id</span> <span class="o">:=</span> <span class="bp">λ</span> <span class="n">α</span><span class="o">,</span> <span class="n">extract</span><span class="o">,</span>
  <span class="n">comp</span> <span class="o">:=</span> <span class="bp">λ</span> <span class="n">X</span> <span class="n">Y</span> <span class="n">Z</span> <span class="n">f</span> <span class="n">g</span><span class="o">,</span> <span class="n">f</span> <span class="bp">=&gt;=</span> <span class="n">g</span><span class="o">,</span>
  <span class="n">id_comp&#39;</span> <span class="o">:=</span> <span class="n">sorry</span><span class="o">,</span>
  <span class="n">comp_id&#39;</span> <span class="o">:=</span> <span class="n">sorry</span><span class="o">,</span>
  <span class="n">assoc&#39;</span> <span class="o">:=</span> <span class="n">sorry</span> <span class="o">}</span>

<span class="bp">@</span><span class="o">[</span><span class="n">simp</span><span class="o">]</span> <span class="kn">lemma</span> <span class="n">Cokleisli</span><span class="bp">.</span><span class="n">comp_def</span> <span class="o">{</span><span class="n">m</span> <span class="o">:</span> <span class="kt">Type</span><span class="bp">*</span> <span class="bp">→</span> <span class="kt">Type</span><span class="bp">*</span><span class="o">}</span> <span class="o">[</span><span class="n">comonad</span> <span class="n">m</span><span class="o">]</span> <span class="o">(</span><span class="n">α</span> <span class="n">β</span> <span class="n">γ</span> <span class="o">:</span> <span class="n">Cokleisli</span> <span class="n">m</span><span class="o">)</span>
  <span class="o">(</span><span class="n">xs</span> <span class="o">:</span> <span class="n">α</span> <span class="err">⟶</span> <span class="n">β</span><span class="o">)</span> <span class="o">(</span><span class="n">ys</span> <span class="o">:</span> <span class="n">β</span> <span class="err">⟶</span> <span class="n">γ</span><span class="o">)</span> <span class="o">:</span>
  <span class="n">xs</span> <span class="err">≫</span> <span class="n">ys</span> <span class="bp">=</span> <span class="n">xs</span> <span class="bp">=&gt;=</span> <span class="n">ys</span> <span class="o">:=</span> <span class="n">sorry</span>

<span class="kn">variables</span> <span class="o">{</span><span class="n">α</span> <span class="n">β</span> <span class="o">:</span> <span class="kt">Type</span> <span class="n">u</span><span class="o">}</span>

<span class="kn">example</span> <span class="o">{</span><span class="n">m</span><span class="o">}</span> <span class="o">[</span><span class="n">comonad</span> <span class="n">m</span><span class="o">]</span> <span class="o">(</span><span class="n">f</span> <span class="o">:</span> <span class="n">α</span> <span class="err">⟶</span> <span class="n">m</span> <span class="n">α</span><span class="o">)</span> <span class="o">(</span><span class="n">g</span> <span class="o">:</span> <span class="n">m</span> <span class="n">α</span> <span class="err">⟶</span> <span class="n">α</span><span class="o">)</span> <span class="o">:</span> <span class="n">f</span> <span class="err">≫</span> <span class="n">g</span> <span class="bp">=</span> <span class="mi">𝟙</span> <span class="bp">_</span> <span class="o">:=</span>
<span class="k">by</span> <span class="o">{</span> <span class="n">simp</span><span class="o">,</span> <span class="o">}</span>
<span class="c1">-- deep recursion was detected at &#39;expression replacer&#39; (potential</span>
<span class="c1">-- solution: increase stack space in your system)</span>
</pre></div>

#### [ Simon Hudon (Jan 23 2019 at 00:22)](https://leanprover.zulipchat.com/#narrow/stream/113488-general/topic/simp%20canonize/near/156649296):
<p>If you comment out <code>Cokleisli.comp_def</code>, the error disappears</p>


{% endraw %}
