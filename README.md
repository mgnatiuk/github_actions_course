# github_actions_course

- We can trigger workflows manually via Github CLI or Github REST API

```yaml
gh workflow run main.yaml \
-f name = Miko
- F data=@myfile.txt