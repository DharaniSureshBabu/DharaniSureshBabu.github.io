
```python
# Get list of NDVI tif file paths
ndvi_paths = sorted(glob(os.path.join(data_dir, 'wi-ndvi', '*', '*NDVI*.tif')))
len(ndvi_paths)
```




    30




```python
scale_factor = 10000
doy_start = -19
doy_end = -12
```


```python
ndvi_das = []
for ndvi_path in ndvi_paths:
    # Get date from file name
    doy = ndvi_path[doy_start:doy_end]
    date = pd.to_datetime(doy, format='%Y%j')

    # Open dataset
    da = rxr.open_rasterio(ndvi_path, masked=True).squeeze()

    # Add date dimension and clean up metadata
    da = da.assign_coords({'date': date})
    da = da.expand_dims({'date': 1})
    da.name = 'NDVI'

    # Multiple by scale factor
    da = da / scale_factor

    # Prepare for concatenation
    ndvi_das.append(da)

len(ndvi_das)
```




    30




```python
ndvi_da = xr.combine_by_coords(ndvi_das, coords=['date'])
ndvi_da
```




<div><svg style="position: absolute; width: 0; height: 0; overflow: hidden">
<defs>
<symbol id="icon-database" viewBox="0 0 32 32">
<path d="M16 0c-8.837 0-16 2.239-16 5v4c0 2.761 7.163 5 16 5s16-2.239 16-5v-4c0-2.761-7.163-5-16-5z"></path>
<path d="M16 17c-8.837 0-16-2.239-16-5v6c0 2.761 7.163 5 16 5s16-2.239 16-5v-6c0 2.761-7.163 5-16 5z"></path>
<path d="M16 26c-8.837 0-16-2.239-16-5v6c0 2.761 7.163 5 16 5s16-2.239 16-5v-6c0 2.761-7.163 5-16 5z"></path>
</symbol>
<symbol id="icon-file-text2" viewBox="0 0 32 32">
<path d="M28.681 7.159c-0.694-0.947-1.662-2.053-2.724-3.116s-2.169-2.030-3.116-2.724c-1.612-1.182-2.393-1.319-2.841-1.319h-15.5c-1.378 0-2.5 1.121-2.5 2.5v27c0 1.378 1.122 2.5 2.5 2.5h23c1.378 0 2.5-1.122 2.5-2.5v-19.5c0-0.448-0.137-1.23-1.319-2.841zM24.543 5.457c0.959 0.959 1.712 1.825 2.268 2.543h-4.811v-4.811c0.718 0.556 1.584 1.309 2.543 2.268zM28 29.5c0 0.271-0.229 0.5-0.5 0.5h-23c-0.271 0-0.5-0.229-0.5-0.5v-27c0-0.271 0.229-0.5 0.5-0.5 0 0 15.499-0 15.5 0v7c0 0.552 0.448 1 1 1h7v19.5z"></path>
<path d="M23 26h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
<path d="M23 22h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
<path d="M23 18h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
</symbol>
</defs>
</svg>
<style>/* CSS stylesheet for displaying xarray objects in jupyterlab.
 *
 */

:root {
  --xr-font-color0: var(--jp-content-font-color0, rgba(0, 0, 0, 1));
  --xr-font-color2: var(--jp-content-font-color2, rgba(0, 0, 0, 0.54));
  --xr-font-color3: var(--jp-content-font-color3, rgba(0, 0, 0, 0.38));
  --xr-border-color: var(--jp-border-color2, #e0e0e0);
  --xr-disabled-color: var(--jp-layout-color3, #bdbdbd);
  --xr-background-color: var(--jp-layout-color0, white);
  --xr-background-color-row-even: var(--jp-layout-color1, white);
  --xr-background-color-row-odd: var(--jp-layout-color2, #eeeeee);
}

html[theme=dark],
body[data-theme=dark],
body.vscode-dark {
  --xr-font-color0: rgba(255, 255, 255, 1);
  --xr-font-color2: rgba(255, 255, 255, 0.54);
  --xr-font-color3: rgba(255, 255, 255, 0.38);
  --xr-border-color: #1F1F1F;
  --xr-disabled-color: #515151;
  --xr-background-color: #111111;
  --xr-background-color-row-even: #111111;
  --xr-background-color-row-odd: #313131;
}

.xr-wrap {
  display: block !important;
  min-width: 300px;
  max-width: 700px;
}

.xr-text-repr-fallback {
  /* fallback to plain text repr when CSS is not injected (untrusted notebook) */
  display: none;
}

.xr-header {
  padding-top: 6px;
  padding-bottom: 6px;
  margin-bottom: 4px;
  border-bottom: solid 1px var(--xr-border-color);
}

.xr-header > div,
.xr-header > ul {
  display: inline;
  margin-top: 0;
  margin-bottom: 0;
}

.xr-obj-type,
.xr-array-name {
  margin-left: 2px;
  margin-right: 10px;
}

.xr-obj-type {
  color: var(--xr-font-color2);
}

.xr-sections {
  padding-left: 0 !important;
  display: grid;
  grid-template-columns: 150px auto auto 1fr 20px 20px;
}

.xr-section-item {
  display: contents;
}

.xr-section-item input {
  display: none;
}

.xr-section-item input + label {
  color: var(--xr-disabled-color);
}

.xr-section-item input:enabled + label {
  cursor: pointer;
  color: var(--xr-font-color2);
}

.xr-section-item input:enabled + label:hover {
  color: var(--xr-font-color0);
}

.xr-section-summary {
  grid-column: 1;
  color: var(--xr-font-color2);
  font-weight: 500;
}

.xr-section-summary > span {
  display: inline-block;
  padding-left: 0.5em;
}

.xr-section-summary-in:disabled + label {
  color: var(--xr-font-color2);
}

.xr-section-summary-in + label:before {
  display: inline-block;
  content: '►';
  font-size: 11px;
  width: 15px;
  text-align: center;
}

.xr-section-summary-in:disabled + label:before {
  color: var(--xr-disabled-color);
}

.xr-section-summary-in:checked + label:before {
  content: '▼';
}

.xr-section-summary-in:checked + label > span {
  display: none;
}

.xr-section-summary,
.xr-section-inline-details {
  padding-top: 4px;
  padding-bottom: 4px;
}

.xr-section-inline-details {
  grid-column: 2 / -1;
}

.xr-section-details {
  display: none;
  grid-column: 1 / -1;
  margin-bottom: 5px;
}

.xr-section-summary-in:checked ~ .xr-section-details {
  display: contents;
}

.xr-array-wrap {
  grid-column: 1 / -1;
  display: grid;
  grid-template-columns: 20px auto;
}

.xr-array-wrap > label {
  grid-column: 1;
  vertical-align: top;
}

.xr-preview {
  color: var(--xr-font-color3);
}

.xr-array-preview,
.xr-array-data {
  padding: 0 5px !important;
  grid-column: 2;
}

.xr-array-data,
.xr-array-in:checked ~ .xr-array-preview {
  display: none;
}

.xr-array-in:checked ~ .xr-array-data,
.xr-array-preview {
  display: inline-block;
}

.xr-dim-list {
  display: inline-block !important;
  list-style: none;
  padding: 0 !important;
  margin: 0;
}

.xr-dim-list li {
  display: inline-block;
  padding: 0;
  margin: 0;
}

.xr-dim-list:before {
  content: '(';
}

.xr-dim-list:after {
  content: ')';
}

.xr-dim-list li:not(:last-child):after {
  content: ',';
  padding-right: 5px;
}

.xr-has-index {
  font-weight: bold;
}

.xr-var-list,
.xr-var-item {
  display: contents;
}

.xr-var-item > div,
.xr-var-item label,
.xr-var-item > .xr-var-name span {
  background-color: var(--xr-background-color-row-even);
  margin-bottom: 0;
}

.xr-var-item > .xr-var-name:hover span {
  padding-right: 5px;
}

.xr-var-list > li:nth-child(odd) > div,
.xr-var-list > li:nth-child(odd) > label,
.xr-var-list > li:nth-child(odd) > .xr-var-name span {
  background-color: var(--xr-background-color-row-odd);
}

.xr-var-name {
  grid-column: 1;
}

.xr-var-dims {
  grid-column: 2;
}

.xr-var-dtype {
  grid-column: 3;
  text-align: right;
  color: var(--xr-font-color2);
}

.xr-var-preview {
  grid-column: 4;
}

.xr-index-preview {
  grid-column: 2 / 5;
  color: var(--xr-font-color2);
}

.xr-var-name,
.xr-var-dims,
.xr-var-dtype,
.xr-preview,
.xr-attrs dt {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  padding-right: 10px;
}

.xr-var-name:hover,
.xr-var-dims:hover,
.xr-var-dtype:hover,
.xr-attrs dt:hover {
  overflow: visible;
  width: auto;
  z-index: 1;
}

.xr-var-attrs,
.xr-var-data,
.xr-index-data {
  display: none;
  background-color: var(--xr-background-color) !important;
  padding-bottom: 5px !important;
}

.xr-var-attrs-in:checked ~ .xr-var-attrs,
.xr-var-data-in:checked ~ .xr-var-data,
.xr-index-data-in:checked ~ .xr-index-data {
  display: block;
}

.xr-var-data > table {
  float: right;
}

.xr-var-name span,
.xr-var-data,
.xr-index-name div,
.xr-index-data,
.xr-attrs {
  padding-left: 25px !important;
}

.xr-attrs,
.xr-var-attrs,
.xr-var-data,
.xr-index-data {
  grid-column: 1 / -1;
}

dl.xr-attrs {
  padding: 0;
  margin: 0;
  display: grid;
  grid-template-columns: 125px auto;
}

.xr-attrs dt,
.xr-attrs dd {
  padding: 0;
  margin: 0;
  float: left;
  padding-right: 10px;
  width: auto;
}

.xr-attrs dt {
  font-weight: normal;
  grid-column: 1;
}

.xr-attrs dt:hover span {
  display: inline-block;
  background: var(--xr-background-color);
  padding-right: 10px;
}

.xr-attrs dd {
  grid-column: 2;
  white-space: pre-wrap;
  word-break: break-all;
}

.xr-icon-database,
.xr-icon-file-text2,
.xr-no-icon {
  display: inline-block;
  vertical-align: middle;
  width: 1em;
  height: 1.5em !important;
  stroke-width: 0;
  stroke: currentColor;
  fill: currentColor;
}
</style><pre class='xr-text-repr-fallback'>&lt;xarray.Dataset&gt;
Dimensions:      (x: 2921, y: 2203, date: 18)
Coordinates:
    band         int64 1
  * x            (x) float64 -92.89 -92.89 -92.88 ... -86.81 -86.81 -86.81
  * y            (y) float64 47.08 47.08 47.08 47.07 ... 42.5 42.5 42.49 42.49
    spatial_ref  int64 0
  * date         (date) datetime64[ns] 2018-06-26 2018-07-12 ... 2023-07-28
Data variables:
    NDVI         (date, y, x) float32 0.8731 0.869 0.8804 0.8804 ... nan nan nan</pre><div class='xr-wrap' style='display:none'><div class='xr-header'><div class='xr-obj-type'>xarray.Dataset</div></div><ul class='xr-sections'><li class='xr-section-item'><input id='section-9f978b5c-8d44-471f-a95c-78d925d39fbc' class='xr-section-summary-in' type='checkbox' disabled ><label for='section-9f978b5c-8d44-471f-a95c-78d925d39fbc' class='xr-section-summary'  title='Expand/collapse section'>Dimensions:</label><div class='xr-section-inline-details'><ul class='xr-dim-list'><li><span class='xr-has-index'>x</span>: 2921</li><li><span class='xr-has-index'>y</span>: 2203</li><li><span class='xr-has-index'>date</span>: 18</li></ul></div><div class='xr-section-details'></div></li><li class='xr-section-item'><input id='section-0a9b7d3f-4ebf-432f-a175-8c744efca622' class='xr-section-summary-in' type='checkbox'  checked><label for='section-0a9b7d3f-4ebf-432f-a175-8c744efca622' class='xr-section-summary' >Coordinates: <span>(5)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-var-name'><span>band</span></div><div class='xr-var-dims'>()</div><div class='xr-var-dtype'>int64</div><div class='xr-var-preview xr-preview'>1</div><input id='attrs-3839d5a2-ac0c-41fd-826a-16606abecefe' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-3839d5a2-ac0c-41fd-826a-16606abecefe' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-43bec995-0eb2-45a4-81c2-38e06a3bdc55' class='xr-var-data-in' type='checkbox'><label for='data-43bec995-0eb2-45a4-81c2-38e06a3bdc55' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array(1)</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>x</span></div><div class='xr-var-dims'>(x)</div><div class='xr-var-dtype'>float64</div><div class='xr-var-preview xr-preview'>-92.89 -92.89 ... -86.81 -86.81</div><input id='attrs-f1cee7cc-938c-4906-a18c-8465352a9bb5' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-f1cee7cc-938c-4906-a18c-8465352a9bb5' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-299d63d6-ad6c-4136-b44b-ae81f9512254' class='xr-var-data-in' type='checkbox'><label for='data-299d63d6-ad6c-4136-b44b-ae81f9512254' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([-92.888542, -92.886458, -92.884375, ..., -86.809375, -86.807292,
       -86.805208])</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>y</span></div><div class='xr-var-dims'>(y)</div><div class='xr-var-dtype'>float64</div><div class='xr-var-preview xr-preview'>47.08 47.08 47.08 ... 42.49 42.49</div><input id='attrs-e38c11eb-4ecc-424a-9ed0-6e2aab9299b4' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-e38c11eb-4ecc-424a-9ed0-6e2aab9299b4' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-d4d1dc6e-1f7b-4cab-b622-b61779302121' class='xr-var-data-in' type='checkbox'><label for='data-d4d1dc6e-1f7b-4cab-b622-b61779302121' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([47.080208, 47.078125, 47.076042, ..., 42.496875, 42.494792, 42.492708])</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span>spatial_ref</span></div><div class='xr-var-dims'>()</div><div class='xr-var-dtype'>int64</div><div class='xr-var-preview xr-preview'>0</div><input id='attrs-a3581683-6c94-424e-a285-c5c1ec75eeef' class='xr-var-attrs-in' type='checkbox' ><label for='attrs-a3581683-6c94-424e-a285-c5c1ec75eeef' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-678a2b6c-bf09-4d95-9ca8-74db685200d4' class='xr-var-data-in' type='checkbox'><label for='data-678a2b6c-bf09-4d95-9ca8-74db685200d4' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'><dt><span>crs_wkt :</span></dt><dd>GEOGCS[&quot;WGS 84&quot;,DATUM[&quot;WGS_1984&quot;,SPHEROID[&quot;WGS 84&quot;,6378137,298.257223563,AUTHORITY[&quot;EPSG&quot;,&quot;7030&quot;]],AUTHORITY[&quot;EPSG&quot;,&quot;6326&quot;]],PRIMEM[&quot;Greenwich&quot;,0,AUTHORITY[&quot;EPSG&quot;,&quot;8901&quot;]],UNIT[&quot;degree&quot;,0.0174532925199433,AUTHORITY[&quot;EPSG&quot;,&quot;9122&quot;]],AXIS[&quot;Latitude&quot;,NORTH],AXIS[&quot;Longitude&quot;,EAST],AUTHORITY[&quot;EPSG&quot;,&quot;4326&quot;]]</dd><dt><span>semi_major_axis :</span></dt><dd>6378137.0</dd><dt><span>semi_minor_axis :</span></dt><dd>6356752.314245179</dd><dt><span>inverse_flattening :</span></dt><dd>298.257223563</dd><dt><span>reference_ellipsoid_name :</span></dt><dd>WGS 84</dd><dt><span>longitude_of_prime_meridian :</span></dt><dd>0.0</dd><dt><span>prime_meridian_name :</span></dt><dd>Greenwich</dd><dt><span>geographic_crs_name :</span></dt><dd>WGS 84</dd><dt><span>horizontal_datum_name :</span></dt><dd>World Geodetic System 1984</dd><dt><span>grid_mapping_name :</span></dt><dd>latitude_longitude</dd><dt><span>spatial_ref :</span></dt><dd>GEOGCS[&quot;WGS 84&quot;,DATUM[&quot;WGS_1984&quot;,SPHEROID[&quot;WGS 84&quot;,6378137,298.257223563,AUTHORITY[&quot;EPSG&quot;,&quot;7030&quot;]],AUTHORITY[&quot;EPSG&quot;,&quot;6326&quot;]],PRIMEM[&quot;Greenwich&quot;,0,AUTHORITY[&quot;EPSG&quot;,&quot;8901&quot;]],UNIT[&quot;degree&quot;,0.0174532925199433,AUTHORITY[&quot;EPSG&quot;,&quot;9122&quot;]],AXIS[&quot;Latitude&quot;,NORTH],AXIS[&quot;Longitude&quot;,EAST],AUTHORITY[&quot;EPSG&quot;,&quot;4326&quot;]]</dd><dt><span>GeoTransform :</span></dt><dd>-92.88958332469498 0.002083333333139592 0.0 47.08124999562164 0.0 -0.002083333333139592</dd></dl></div><div class='xr-var-data'><pre>array(0)</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>date</span></div><div class='xr-var-dims'>(date)</div><div class='xr-var-dtype'>datetime64[ns]</div><div class='xr-var-preview xr-preview'>2018-06-26 ... 2023-07-28</div><input id='attrs-9b51717a-9197-4e97-acdc-f3bf4f8c0bd5' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-9b51717a-9197-4e97-acdc-f3bf4f8c0bd5' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-afa3e3df-fd20-4d32-98bd-4078e44382cb' class='xr-var-data-in' type='checkbox'><label for='data-afa3e3df-fd20-4d32-98bd-4078e44382cb' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([&#x27;2018-06-26T00:00:00.000000000&#x27;, &#x27;2018-07-12T00:00:00.000000000&#x27;,
       &#x27;2018-07-28T00:00:00.000000000&#x27;, &#x27;2019-06-26T00:00:00.000000000&#x27;,
       &#x27;2019-07-12T00:00:00.000000000&#x27;, &#x27;2019-07-28T00:00:00.000000000&#x27;,
       &#x27;2020-06-25T00:00:00.000000000&#x27;, &#x27;2020-07-11T00:00:00.000000000&#x27;,
       &#x27;2020-07-27T00:00:00.000000000&#x27;, &#x27;2021-06-26T00:00:00.000000000&#x27;,
       &#x27;2021-07-12T00:00:00.000000000&#x27;, &#x27;2021-07-28T00:00:00.000000000&#x27;,
       &#x27;2022-06-26T00:00:00.000000000&#x27;, &#x27;2022-07-12T00:00:00.000000000&#x27;,
       &#x27;2022-07-28T00:00:00.000000000&#x27;, &#x27;2023-06-26T00:00:00.000000000&#x27;,
       &#x27;2023-07-12T00:00:00.000000000&#x27;, &#x27;2023-07-28T00:00:00.000000000&#x27;],
      dtype=&#x27;datetime64[ns]&#x27;)</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-4d9417dc-20af-4d02-886e-775ea562cfe7' class='xr-section-summary-in' type='checkbox'  checked><label for='section-4d9417dc-20af-4d02-886e-775ea562cfe7' class='xr-section-summary' >Data variables: <span>(1)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-var-name'><span>NDVI</span></div><div class='xr-var-dims'>(date, y, x)</div><div class='xr-var-dtype'>float32</div><div class='xr-var-preview xr-preview'>0.8731 0.869 0.8804 ... nan nan nan</div><input id='attrs-21744556-c0c4-4755-a5ce-1fe3f483aa9e' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-21744556-c0c4-4755-a5ce-1fe3f483aa9e' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-a3112a98-2965-4d9b-80ec-cc4b8ef95f36' class='xr-var-data-in' type='checkbox'><label for='data-a3112a98-2965-4d9b-80ec-cc4b8ef95f36' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([[[ 0.8731,  0.869 ,  0.8804, ...,     nan,     nan,     nan],
        [ 0.7979,  0.8186,  0.8186, ...,     nan,     nan,     nan],
        [ 0.8661,  0.8635,  0.8635, ...,     nan,     nan,     nan],
        ...,
        [ 0.8436,  0.8601,  0.9003, ...,  0.0189,  0.0189, -0.0147],
        [ 0.8151,  0.8903,  0.8903, ..., -0.1371, -0.1371, -0.1371],
        [ 0.7851,  0.8955,  0.8897, ..., -0.1213, -0.1213, -0.1371]],

       [[ 0.8779,  0.8829,  0.8399, ...,     nan,     nan,     nan],
        [ 0.7645,  0.7683,  0.7683, ...,     nan,     nan,     nan],
        [ 0.8508,  0.8567,  0.8567, ...,     nan,     nan,     nan],
        ...,
        [ 0.884 ,  0.9043,  0.9084, ...,  0.0084,  0.0084,     nan],
        [ 0.8835,  0.9043,  0.9085, ...,  0.5735,  0.5735,  0.0637],
        [ 0.8137,  0.8646,  0.9013, ...,     nan,     nan,     nan]],

       [[ 0.8541,  0.8752,  0.8491, ...,     nan,     nan,     nan],
        [ 0.8069,  0.8069,  0.8069, ...,     nan,     nan,     nan],
        [ 0.8495,  0.8529,  0.8529, ...,     nan,     nan,     nan],
        ...,
...
        [ 0.8327,  0.8737,  0.8777, ..., -0.0265, -0.0265, -0.0251],
        [ 0.8327,  0.8099,  0.7774, ..., -0.0265, -0.0265, -0.0189],
        [ 0.8104,  0.7654,  0.8367, ...,  0.0044,  0.0044, -0.036 ]],

       [[ 0.8669,  0.8419,  0.9002, ...,     nan,     nan,     nan],
        [ 0.8468,  0.8419,  0.8419, ...,     nan,     nan,     nan],
        [ 0.8468,  0.817 ,  0.817 , ...,     nan,     nan,     nan],
        ...,
        [ 0.8776,  0.9085,  0.9085, ...,     nan,     nan,     nan],
        [ 0.8416,  0.8819,  0.9193, ...,     nan,     nan,     nan],
        [ 0.8733,  0.9049,  0.9193, ...,     nan,     nan,     nan]],

       [[ 0.8753,  0.8882,  0.8554, ...,     nan,     nan,     nan],
        [ 0.8316,  0.8072,  0.8072, ...,     nan,     nan,     nan],
        [ 0.8572,  0.8305,  0.8305, ...,     nan,     nan,     nan],
        ...,
        [ 0.776 ,  0.8521,  0.883 , ...,     nan,     nan,     nan],
        [ 0.7984,  0.8445,  0.8981, ...,     nan,     nan,     nan],
        [ 0.809 ,  0.809 ,  0.8974, ...,     nan,     nan,     nan]]],
      dtype=float32)</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-e3817d92-e9d7-4faf-a019-bd0132a8e550' class='xr-section-summary-in' type='checkbox'  ><label for='section-e3817d92-e9d7-4faf-a019-bd0132a8e550' class='xr-section-summary' >Indexes: <span>(3)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-index-name'><div>x</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-16f04920-5701-421b-9b05-0cfa3d3d846d' class='xr-index-data-in' type='checkbox'/><label for='index-16f04920-5701-421b-9b05-0cfa3d3d846d' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(Index([-92.88854165802842, -92.88645832469527, -92.88437499136214,
         -92.882291658029, -92.88020832469586, -92.87812499136271,
       -92.87604165802958, -92.87395832469645,  -92.8718749913633,
       -92.86979165803015,
       ...
       -86.82395832525907, -86.82187499192592, -86.81979165859278,
       -86.81770832525964, -86.81562499192651, -86.81354165859337,
       -86.81145832526022, -86.80937499192709, -86.80729165859395,
        -86.8052083252608],
      dtype=&#x27;float64&#x27;, name=&#x27;x&#x27;, length=2921))</pre></div></li><li class='xr-var-item'><div class='xr-index-name'><div>y</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-9d9c9015-020e-424d-a5bd-5b379bb94488' class='xr-index-data-in' type='checkbox'/><label for='index-9d9c9015-020e-424d-a5bd-5b379bb94488' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(Index([ 47.08020832895507,  47.07812499562193,  47.07604166228879,
        47.07395832895565,  47.07187499562251,  47.06979166228937,
        47.06770832895623,  47.06562499562309,  47.06354166228995,
        47.06145832895681,
       ...
        42.51145832937994,   42.5093749960468,  42.50729166271366,
        42.50520832938052,  42.50312499604738,  42.50104166271424,
       42.498958329381104, 42.496874996047964, 42.494791662714825,
       42.492708329381685],
      dtype=&#x27;float64&#x27;, name=&#x27;y&#x27;, length=2203))</pre></div></li><li class='xr-var-item'><div class='xr-index-name'><div>date</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-812a9425-02c9-4960-a71d-a748dde7e9c5' class='xr-index-data-in' type='checkbox'/><label for='index-812a9425-02c9-4960-a71d-a748dde7e9c5' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(DatetimeIndex([&#x27;2018-06-26&#x27;, &#x27;2018-07-12&#x27;, &#x27;2018-07-28&#x27;, &#x27;2019-06-26&#x27;,
               &#x27;2019-07-12&#x27;, &#x27;2019-07-28&#x27;, &#x27;2020-06-25&#x27;, &#x27;2020-07-11&#x27;,
               &#x27;2020-07-27&#x27;, &#x27;2021-06-26&#x27;, &#x27;2021-07-12&#x27;, &#x27;2021-07-28&#x27;,
               &#x27;2022-06-26&#x27;, &#x27;2022-07-12&#x27;, &#x27;2022-07-28&#x27;, &#x27;2023-06-26&#x27;,
               &#x27;2023-07-12&#x27;, &#x27;2023-07-28&#x27;],
              dtype=&#x27;datetime64[ns]&#x27;, name=&#x27;date&#x27;, freq=None))</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-00f359e1-e28d-4185-b1e2-d343366540a5' class='xr-section-summary-in' type='checkbox' disabled ><label for='section-00f359e1-e28d-4185-b1e2-d343366540a5' class='xr-section-summary'  title='Expand/collapse section'>Attributes: <span>(0)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><dl class='xr-attrs'></dl></div></li></ul></div></div>


