---


---

<h4 id="bigquery--자주-사용하는-자료형-변환-함수-정리">BigQuery  자주 사용하는 자료형 변환 함수 정리</h4>
<ul>
<li>본 포스팅은 BigQuery사용 중 자주 이용하고 헷갈려하는 변환 함수들에 관하여 정리한 글입니다.</li>
<li>GCP 공식 가이드를 기반으로 작성됩니다.</li>
<li>지속적으로 기록될 예정입니다.</li>
</ul>
<hr>
<h3 id="format_datatype-함수-">[ 1. FORMAT_(DataType) 함수 ]</h3>
<pre class=" language-sql"><code class="prism  language-sql">FORMAT_<span class="token punctuation">[</span><span class="token keyword">DATE</span><span class="token operator">/</span><span class="token keyword">DATETIME</span><span class="token operator">/</span><span class="token keyword">TIMESTAMP</span><span class="token punctuation">]</span><span class="token punctuation">(</span> <span class="token punctuation">[</span>format_string<span class="token punctuation">]</span><span class="token punctuation">,</span> <span class="token punctuation">[</span><span class="token keyword">DATE</span><span class="token operator">/</span><span class="token keyword">DATETIME</span><span class="token operator">/</span>TIMESTAMP_expr<span class="token punctuation">]</span> <span class="token punctuation">)</span>
</code></pre>
<ul>
<li>지정된 <code>format_string</code>에 따라 타임스탬프의 형식을 지정한다.<br>ex1) 20201021 DATE 자료형 =&gt; 2020-10-21<code>STRING</code> 자료형<br>ex2) 20201021 00:00:00 DATETIME 자료형 =&gt; 2020-10-21 <code>STRING</code> 자료형<br>ex3)2020-10-21 00:00:00 UTC TIMESTAMP 자료형 =&gt; 2020-10-21 <code>STRING</code> 자료형</li>
<li>반환되는 데이터 유형은 ☑<code>STRING</code>이다.</li>
</ul>
<hr>
<h3 id="parse_datatype-함수-">[ 2. PARSE_(DataType) 함수 ]</h3>
<pre class=" language-sql"><code class="prism  language-sql">PARSE_<span class="token punctuation">[</span><span class="token keyword">DATE</span><span class="token operator">/</span><span class="token keyword">DATETIME</span><span class="token operator">/</span><span class="token keyword">TIMESTAMP</span><span class="token punctuation">]</span><span class="token punctuation">(</span> <span class="token punctuation">[</span>format_string<span class="token punctuation">]</span><span class="token punctuation">,</span> <span class="token punctuation">[</span><span class="token keyword">DATE</span><span class="token operator">/</span><span class="token keyword">DATETIME</span><span class="token operator">/</span>TIMESTAMP_string<span class="token punctuation">]</span> <span class="token punctuation">)</span>
</code></pre>
<ul>
<li>DATE/DATETIME/TIMESTAMP의 <code>STRING</code> 표현을 각각 ☑<code>DATE/DATETIME/TIMESTAMP</code>객체로 반환한다.</li>
<li><u>[DATE/DATETIME/TIMESTAMP_string]의 각 요소는 [format_string]에 해당하는 요소여야 한다.</u></li>
</ul>
<hr>
<h3 id="timestamp_micros-함수-">[ 3. TIMESTAMP_MICROS 함수 ]</h3>
<pre class=" language-sql"><code class="prism  language-sql">TIMESTAMP_MICROS<span class="token punctuation">(</span>int64_expression<span class="token punctuation">)</span>
</code></pre>
<ul>
<li><code>int64-expression</code>( ex. 1230219000000000 )을 마이크로초 수로 해석하여 <code>TIMESTAMP</code>형태로 반환한다. <br>ex) 1230219000000000 =&gt; 2008-12-25 15:30:00 UTC</li>
</ul>

