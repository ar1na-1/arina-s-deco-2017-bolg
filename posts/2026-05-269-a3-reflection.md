# A3 Reflection: Looking Back at My Goal-Oriented Feedback Hub

For my final prototype, I built a goal-oriented feedback hub for students and designers who want more useful feedback on their ideas or prototypes. Users can create a post, upload a prototype image, choose what kind of feedback they want, and receive comments from other users. When I first planned the project, I mainly thought about the visible interaction: someone posts an idea, someone else gives feedback, and the original poster can read it. After building and testing it, I realised that the harder part was making the whole flow behave reliably. A feedback platform only feels meaningful if posts, images, comments and delete actions all work in a consistent way.

Overall, I think the final prototype is much stronger than my first version. At the beginning, many pages were static and some buttons only looked interactive. By the end, I had implemented database-backed post creation, image upload, dynamic post detail pages, saved feedback, delete actions, account-style pages, responsive layout, and clearer navigation. It is still a prototype rather than a finished product, but it now demonstrates the main behaviour of the system rather than only showing a visual mock-up.

---

## Performance and Technical Behaviour

I evaluated the technical behaviour of the application by repeatedly testing the main user flows in the local development environment. I tested logging in, creating a text-only post, creating a post with an uploaded image, opening the uploaded post, submitting feedback, refreshing the page, deleting feedback, deleting a post, and using the Account, My Posts and My Feedback pages. I also ran `npm run build` after major changes to check that the TypeScript build still passed. This was useful because some problems did not appear while I was editing individual pages, but became clear when the application was rebuilt or restarted.

The strongest technical improvement is that the main data now persists. When a user creates a post, it is stored in the SQLite database and appears again after the page is refreshed. When a user uploads an image, the image file is saved and its path is stored with the post. This allows the same uploaded image to appear on both the homepage and the post detail page. Feedback also persists, so comments do not disappear after refreshing. This changed the project from a mostly visual prototype into something closer to a working web application.

![Evidence: npm run build passing successfully](./images/build-passed.png)

One technical issue I had to solve was image upload. At one point, the create form had an upload input, but it did not actually save or display the image. I temporarily removed the upload field so the basic text post creation could work first, then added image upload back properly using `multipart/form-data`, backend file handling, and an `image_path` field in the posts table. This helped me understand that a form element is not truly functional unless the backend data flow supports it as well.

Another issue was CSS not updating in the browser. Earlier in the project, I thought the stylesheet was broken because my latest changes were not showing. Later, I found that the browser was using a cached version of the CSS. After disabling cache in DevTools and moving the styling back into `public/styles.css`, the interface became easier to control. This also improved maintainability because the CSS was no longer spread across large inline style blocks inside the templates.

![Evidence: uploaded image appearing on the homepage and detail page](./images/uploaded-post-homepage.png)

The main technical limitation is that the application still uses a local SQLite database and local uploaded files. This is acceptable for a coursework prototype, but it would not scale well as a deployed multi-user system. For example, deleting a post removes the post and its feedback from the database, but a stronger production version should also clean up unused uploaded image files. I would also add more validation for image file size, accepted file types, and user ownership before allowing delete actions.

---

## User Experience and Accessibility

The main user experience goal was to make feedback more focused. Instead of letting users leave vague comments like “looks good”, the application asks the post author to choose a feedback goal and describe what they want help with. On the detail page, the feedback form is split into three questions: what feels clear, what feels confusing, and what could be improved. I think this supports better feedback because reviewers are not starting from a blank comment box. The structure guides them to give more useful and actionable responses.

Navigation improved a lot during development. Earlier, the top navigation links looked clickable but did not actually go anywhere. I replaced these fake links with real pages: Account, My Posts and My Feedback. This made the application feel more complete because users can now review their own posts and feedback history. I also added a visible Back to Home link on these pages because relying only on the logo was not clear enough. This was a small change, but it made the navigation flow feel more predictable.

![Evidence: Account, My Posts and My Feedback pages](./images/dashboard-pages.png)

For accessibility, I considered WCAG through practical interaction issues rather than treating it as only a checklist. The most relevant guideline for my prototype is WCAG 3.3 Input Assistance, because the app depends heavily on users entering information. I added clear labels for form fields, required fields for important inputs, and structured feedback questions. I also added confirmation prompts before destructive actions such as deleting a post or comment. These decisions support users by reducing confusion and lowering the chance of accidental data loss.

The prototype also uses meaningful alt text for images and real buttons or links for interactive elements. This is important because fake clickable elements can be confusing and harder to navigate with a keyboard or assistive technology. I tested keyboard navigation informally by tabbing through forms, links and buttons. The main controls were reachable, but I would still want to run a more formal accessibility audit using Lighthouse, axe or WAVE before claiming full WCAG AA compliance. At this stage, I would describe the prototype as WCAG-informed rather than fully audited.

Responsive layout was another important UX improvement. The desktop version uses cards, grid layouts and two-column detail pages, but that layout became cramped when the browser width was reduced. I added media queries so post cards, create forms, detail pages and dashboard pages stack vertically on smaller screens. By resizing the browser window, I checked that the interface stayed readable and avoided obvious horizontal overflow.

![Evidence: responsive layout on a narrow screen](./images/responsive-layout.png)

The main UX weakness is that the interface is still quite simple. For example, My Feedback shows feedback submitted by the user, but uploaded-post feedback is still connected to a post ID rather than a readable post title. This works technically, but it is not ideal for users. If there were many posts, users might not remember which post each ID refers to. In a future version, I would join the feedback data with the post data so the dashboard could display the actual post title and image thumbnail.

---

## Retrospective Assessment of Functional Requirements

My original functional requirements were that users should be able to create a post, upload or display a prototype image, request targeted feedback, view posts, and submit feedback. Looking back, these were the right core requirements, but I underestimated how much implementation work was required to make them reliable. A feature such as “submit feedback” sounds simple in planning, but in practice it requires routes, form names, controller logic, database tables, validation, and template updates.

The create-post requirement was successfully implemented. Users can enter a title, describe what feedback they want, choose a feedback goal, add a description, and optionally upload an image. The post then appears on the homepage and opens into its own dynamic detail page. This matched my original plan quite closely, although the implementation became more complex once image upload and database persistence were included.

The feedback requirement became stronger than my original plan. At first, I imagined feedback as something that could simply appear on the page. Later, I realised that feedback needed to be stored in the database to make the platform meaningful. The final prototype stores feedback for user-created posts and for the four example posts. Users can submit feedback, refresh the page, and still see the comment. I also added delete comment actions, which were not in my first plan, but became necessary once comments became persistent.

![Evidence: saved feedback remaining after refresh](./images/saved-feedback.png)

One feature that changed was the checklist page. Earlier, the checklist and publishing flow used an alert, which made it feel like a fake final step. Since the create form now actually creates and saves posts, the checklist is less central to the main system. If I continued the project, I would either remove the checklist flow or redesign it as a real pre-submit review page where users can check their post before saving it.

The account-related pages were not part of my earliest plan. However, adding Account, My Posts and My Feedback helped remove fake navigation and made the system feel more complete. This was a useful scope change because it supported the feedback platform concept instead of adding an unrelated feature.

---

## Critical Reflection and Future Improvements

The biggest lesson I learned is that “working” has different levels. At first, I felt satisfied if a page looked right or if a button could be clicked. Later, I realised that for a web application, a feature only really works when the action changes data in a reliable and expected way. Creating posts, uploading images, submitting feedback, deleting comments and showing dashboard pages all required both frontend and backend changes. This helped me understand the connection between interaction design and implementation much more clearly.

I also learned that scope needs to be managed carefully. Every new feature creates follow-up requirements. Once I added image upload, I also needed to display uploaded images on the homepage and detail page. Once I added persistent feedback, I needed delete actions and feedback history pages. This made the project more realistic, but it also showed me why it is better to finish one complete user flow before adding another.

If I had more time, my first improvement would be better data ownership and permission checks. At the moment, delete buttons exist for posts and comments, but a more responsible system should check whether the current user owns the content before allowing deletion. This would make the application safer and more realistic.

My second improvement would be cleaner CSS organisation. The interface is visually consistent, but the stylesheet has become quite long. I would split it into clearer sections or components, such as layout, forms, cards, detail pages and dashboard pages. This would make the project easier to maintain and reduce the chance of accidental styling conflicts.

My third improvement would be stronger evaluation. I manually tested the main flows and checked responsive behaviour, but I would like to run Lighthouse and do a short usability test with another student. For example, I could ask them to create a post, submit feedback, delete a comment, and then find their feedback later. Watching where they hesitate would give stronger evidence for future design decisions.

Overall, I think the final prototype achieved the main goal of supporting goal-oriented peer feedback. It is not perfect, but it moved from a static visual prototype into a functioning web application with persistence, upload, feedback, deletion, navigation and responsive layout. This project helped me understand that interaction design and technical implementation are not separate. A design only really works when the system behaviour supports what the user expects to happen.
