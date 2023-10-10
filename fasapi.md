If connection remains unaccessible, don't forget:
```
(menv) ubuntu@reso:~$ uvicorn app:app --host 0.0.0.0 --port 8917
```

this will allow connections from any host
