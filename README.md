Script that I used to setup redirects

```bash
# pattern is yyyy-mm-dd-<title>.md
for file in *.md ; do 
    year=$(echo $file | cut -c1-4) 
    name1=$(echo $file | cut -c12-)
    name=${name1%.md}
    url="https://intmaker.com/$year/$name"
    cat <<EOF > "$file"
    ---
    redirect_to: "$url"
    ---
    EOF
done
```
