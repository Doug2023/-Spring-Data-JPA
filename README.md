# -Spring-Data-JPA

# GitHub Actions to automate GitHub issues for Spring Data Project Management
name: Spring Data GitHub Issues
on:
  issues:
    types: [opened, edited, reopened]
  issue_comment:
    types: [created]
  pull_request_target:
    types: [opened, edited, reopened]
jobs:
  Inbox:
    runs-on: ubuntu-latest
    if: github.repository_owner == 'spring-projects' && (github.event.action == 'opened' || github.event.action == 'reopened') && github.event.pull_request == null
    steps:
      - name: Create or Update Issue Card
        uses: peter-evans/create-or-update-project-card@v1.1.2
        with:
          project-name: 'Spring Data'
          column-name: 'Inbox'
          project-location: 'spring-projects'
          token: ${{ secrets.GH_ISSUES_TOKEN_SPRING_DATA }}
  Pull-Request:
    runs-on: ubuntu-latest
    if: github.repository_owner == 'spring-projects' && (github.event.action == 'opened' || github.event.action == 'reopened') && github.event.pull_request != null
    steps:
      - name: Create or Update Pull Request Card
        uses: peter-evans/create-or-update-project-card@v1.1.2
        with:
          project-name: 'Spring Data'
          column-name: 'Review pending'
          project-location: 'spring-projects'
          issue-number: ${{ github.event.pull_request.number }}
          token: ${{ secrets.GH_ISSUES_TOKEN_SPRING_DATA }}
  Feedback-Provided:
    runs-on: ubuntu-latest
    if: github.repository_owner == 'spring-projects' && github.event_name == 'issue_comment' && github.event.action == 'created' && !github.actor == 'spring-issuemaster' && github.event.pull_request == null && github.event.issue.state == 'open' && contains(join(github.event.issue.labels.*.name, ', '), 'waiting-for-feedback')
    if: github.repository_owner == 'spring-projects' && github.event_name == 'issue_comment' && github.event.action == 'created' && github.actor != 'spring-projects-issues' && github.event.pull_request == null && github.event.issue.state == 'open' && contains(toJSON(github.event.issue.labels), 'waiting-for-feedback')
    steps:
      - name: Update Project Card
        uses: peter-evans/create-or-update-project-card@v1.1.2
        with:
          project-name: 'Spring Data'
          column-name: 'Feedback provided'
          project-location: 'spring-projects'
          token: ${{ secrets.GH_ISSUES_TOKEN_SPRING_DATA }}
