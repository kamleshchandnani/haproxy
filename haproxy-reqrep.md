Let's assume we have following line in our HA proxy file:  
`reqrep ^([^\ :]*)\ /api/v1/api-name/(.*)     \1\ /staging/path-name/\2`  
Here is our sample domain:  
`https://example.com/api/v1/api-name/` 


The goal here is to rewrite `/api/v1/api-name/` to `/staging/path-name/` leaving anything else unchanged.

Breaking the Regex and understanding in parts:  
There are basically 3 parts in the regex:
1. `^([^\ :]*)` -- Match a single character not present in the list below `[^\ :]*` i.e blank space ` ` and colon `:`
2. `/api/v1/api-name/` -- Match the whole path string as it is including forward slashes
3. `(.*)` -- Matches anything after the 2nd group, basically for capturing query params.

Coming to our example when the request comes to HA we'll have it in following form:
1. Host: `example.com`
2. Path: `/api/v1/psychological-triggers`
3. Query params if any

Now let's analyse it with our regex part by part:
1. `^([^\ :]*)` -- Match a single character not present in the list below `[^\ :]*`.
* Here it matches our host `example.com`
2. `/api/v1/api-name/` -- Match the whole path string as it is including forward slashes.
* Here it matches our path exactly
3. `(.*)` -- Matches anything after the 2nd group, basically for capturing query params.
* We don't have query params so nothing happens here

Let's understand how replace works. This is our regex for it `\1\ /staging/path-name/\2`. Let's break it and understand what each part means:
1. `\1` -- Keep part 1 from our regex match as it is when rewrting.
* Here `example.com`
2. `/staging/path-name/` -- rewrite the part 2 from the regex match to `/staging/path-name/`.
* Here replace `/api/v1/api-name/` with `/staging/path-name/`
3. `\2` -- Keep the part 3 of the regex as it is. Now it is part 3 as per our descriptions but actually in our regex we have just 2 pattern matching 1 and 3, since 2 is just the exact match, hence part 3 is actually 2 over here.
* Here nothing since we don't have query params.