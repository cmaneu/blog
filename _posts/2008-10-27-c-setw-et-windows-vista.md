---
layout: post
title: C++ / setw et Windows Vista
slug: c-setw-et-windows-vista
date: 2008-10-27 21:28:00Z
---

<p>Je viens d’avoir un problème “de base” en C++. La fonction setw() permet de spécifier la largeur (en caractères) de la sortie standard. Cela peut être utile pour formater une sortie avec une application Console (associé à d’autres éléments tels que left, setprecision(), …)</p>  <p>C’est bien sympa, mais sous Windows Vista/Visual Studio 2008, il ne suffit pas de faire un include de iostream, cela plante à la compilation ! En fait, il suffit d’inclure iomanip et ca fonctionne :</p>  <div class="csharpcode">   <pre class="alt"><span class="lnum">   1:  </span><span class="rem">// TestCpp.cpp : Defines the entry point for the <br />       console application.</span></pre>

  <pre><span class="lnum">   2:  </span>&#160;</pre>

  <pre class="alt"><span class="lnum">   3:  </span>#include <span class="str">&quot;stdafx.h&quot;</span></pre>

  <pre><span class="lnum">   4:  </span>&#160;</pre>

  <pre class="alt"><span class="lnum">   5:  </span>#include &lt;iostream&gt;</pre>

  <pre><span class="lnum">   6:  </span>#include &lt;iomanip&gt;</pre>

  <pre class="alt"><span class="lnum">   7:  </span>&#160;</pre>

  <pre><span class="lnum">   8:  </span><span class="kwrd">using</span> <span class="kwrd">namespace</span> std;</pre>

  <pre class="alt"><span class="lnum">   9:  </span>&#160;</pre>

  <pre><span class="lnum">  10:  </span><span class="kwrd">int</span> _tmain(<span class="kwrd">int</span> argc, _TCHAR* argv[])</pre>

  <pre class="alt"><span class="lnum">  11:  </span>{</pre>

  <pre><span class="lnum">  12:  </span>    <span class="kwrd">char</span> c;</pre>

  <pre class="alt"><span class="lnum">  13:  </span>    cout &lt;&lt;setw(20)&lt;&lt;<span class="str">&quot;coucou&quot;</span>;</pre>

  <pre><span class="lnum">  14:  </span>    cin &gt;&gt; c;</pre>

  <pre class="alt"><span class="lnum">  15:  </span>    <span class="kwrd">return</span> 0;</pre>

  <pre><span class="lnum">  16:  </span>}</pre>

  <pre class="alt"><span class="lnum">  17:  </span>&#160;</pre>
</div>
<style type="text/css">



.csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }</style>

<p>&#160;</p>

<p>Vous pouvez même <a href="http://www.box.net/shared/p9z2ul58vf" target="_blank">télécharger le projet Visual Studio 2008</a> :)</p>
