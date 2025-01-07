# bigdata-save-csv
Save DataFrame to CSV file

```python
import getpass

USER = getpass.getuser()
print("User =", USER)

DOWNLOAD_BASE_PATH = f"/user/{USER}/notebooks/downloads"

def save_csv(df: DataFrame, file_name: str):
    target_file = f"{file_name}"
    folder_to_download = f"{DOWNLOAD_BASE_PATH}/{target_file}"

    print(f"Working at {folder_to_download}...")

    !hdfs dfs -rm -r -f -skipTrash {folder_to_download}/*

    (
        df
        .coalesce(1)
        .write
        .mode('overwrite')
        .options(header='True', delimiter=';')
        .csv(folder_to_download)
    )

    !hdfs dfs -copyToLocal {folder_to_download} {target_file}
    !tar cvzf {target_file}.tar.gz {target_file}/*
    !rm -rf {target_file}
    !hdfs dfs -copyFromLocal {target_file}.tar.gz {folder_to_download}
    !hdfs dfs -rm -r -f -skipTrash {folder_to_download}/*.csv
    !hdfs dfs -rm -r -f -skipTrash {folder_to_download}/_SUCCESS
    !rm {target_file}.tar.gz
    !hdfs dfs -ls {folder_to_download}

    print(f"{target_file} available to download at {folder_to_download}")
```
