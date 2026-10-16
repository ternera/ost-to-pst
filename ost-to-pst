#!/usr/bin/env python3
import argparse
import os
import sys

from aspose.email.storage.pst import FileFormatVersion, PersonalStorage

stats = {"ok": 0, "failed": 0}


def copy_folder(src_store, src_folder, dst_folder):
    for info in src_folder.get_contents():
        try:
            dst_folder.add_message(src_store.extract_message(info))
            stats["ok"] += 1
        except Exception as e:
            stats["failed"] += 1
            print(f"  warning: skipped a message: {e}", file=sys.stderr)
    for sub in src_folder.get_sub_folders():
        name = sub.display_name
        print(f"Copying {name}")
        new = dst_folder.get_sub_folder(name) or dst_folder.add_sub_folder(name)
        copy_folder(src_store, sub, new)


def main():
    p = argparse.ArgumentParser(description="convert ost to pst")
    p.add_argument("ost")
    p.add_argument("pst")
    a = p.parse_args()

    if not os.path.isfile(a.ost):
        sys.exit(f"input not found: {a.ost}")
    if os.path.exists(a.pst):
        sys.exit(f"output already exists: {a.pst}")

    with PersonalStorage.from_file(a.ost, False) as src:
        with PersonalStorage.create(a.pst, FileFormatVersion.UNICODE) as dst:
            copy_folder(src, src.root_folder, dst.root_folder)

    print(f"Done: {stats['ok']} items copied, {stats['failed']} failed -> {a.pst}")


if __name__ == "__main__":
    main()
