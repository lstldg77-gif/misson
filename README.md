def show_menu():
    print("\n" + "=" * 40)
    print("📺 생생정보 네이버 블로그 프롬프트 관리자")
    print("=" * 40)
    print("1. 프롬프트 추가")
    print("2. 전체 프롬프트 목록")
    print("3. 카테고리별 조회")
    print("4. 키워드 검색")
    print("5. 프롬프트 상세보기 (복사용)")
    print("6. 즐겨찾기 관리")
    print("7. 즐겨찾기 목록")
    print("0. 종료")
    print("=" * 40)

def add_prompt():
    print("\n=== 새 프롬프트 등록 ===")
    title = input("프롬프트 제목: ").strip()
    while not title:
        title = input("제목을 입력해주세요: ").strip()

    print("\n[내용 입력 - 입력 완료 시 빈 줄에서 엔터]")
    lines = []
    while True:
        line = input()
        if not line and lines:
            break
        lines.append(line)
    content = "\n".join(lines).strip()

    print("\n카테고리 선택:")
    for idx, cat in enumerate(CATEGORIES, 1):
        print(f"{idx}) {cat}")
    
    cat_idx = input("선택 (번호): ").strip()
    if cat_idx.isdigit() and 1 <= int(cat_idx) <= len(CATEGORIES):
        category = CATEGORIES[int(cat_idx) - 1]
    else:
        category = "기타"

    prompts.append({
        "title": title,
        "content": content,
        "category": category,
        "favorite": False
    })
    print(f"\n✅ '{title}' 프롬프트가 성공적으로 등록되었습니다.")

def show_list():
    print("\n=== 전체 프롬프트 목록 ===")
    if not prompts:
        print("등록된 프롬프트가 없습니다.")
        return

    for idx, p in enumerate(prompts, 1):
        star = " ⭐" if p["favorite"] else ""
        print(f"{idx}. [{p['category']}] {p['title']}{star}")
    print(f"\n총 {len(prompts)}개의 프롬프트")

def show_by_category():
    print("\n=== 카테고리별 조회 ===")
    for idx, cat in enumerate(CATEGORIES, 1):
        print(f"{idx}) {cat}")
    
    choice = input("카테고리 번호 선택: ").strip()
    if choice.isdigit() and 1 <= int(choice) <= len(CATEGORIES):
        selected_cat = CATEGORIES[int(choice) - 1]
        filtered = [p for p in prompts if p["category"] == selected_cat]
        
        print(f"\n[{selected_cat}] 프롬프트 목록:")
        if not filtered:
            print("해당 카테고리에 등록된 프롬프트가 없습니다.")
        else:
            for idx, p in enumerate(filtered, 1):
                star = " ⭐" if p["favorite"] else ""
                print(f"{idx}. {p['title']}{star}")
            print(f"\n총 {len(filtered)}개")
    else:
        print("❌ 잘못된 번호입니다.")

def search_prompt():
    print("\n=== 프롬프트 검색 ===")
    keyword = input("검색어 입력 (제목/내용): ").strip().lower()
    
    results = [p for p in prompts if keyword in p["title"].lower() or keyword in p["content"].lower()]
    
    if not results:
        print("❌ 검색 결과가 없습니다.")
    else:
        print(f"\n'{keyword}' 검색 결과:")
        for idx, p in enumerate(results, 1):
            star = " ⭐" if p["favorite"] else ""
            print(f"{idx}. [{p['category']}] {p['title']}{star}")
        print(f"\n총 {len(results)}개 발견")

def view_detail():
    print("\n=== 프롬프트 상세 보기 ===")
    show_list()
    num = input("\n확인할 프롬프트 번호 선택: ").strip()
    
    if num.isdigit() and 1 <= int(num) <= len(prompts):
        p = prompts[int(num) - 1]
        star = "⭐" if p["favorite"] else "❌"
        print("\n" + "─" * 40)
        print(f"📌 제목: {p['title']}")
        print(f"📂 카테고리: {p['category']}")
        print(f"⭐ 즐겨찾기: {star}")
        print("─" * 40)
        print("📝 프롬프트 내용 (ChatGPT에 바로 복사/붙여넣기 하세요):\n")
        print(p["content"])
        print("─" * 40)
    else:
        print("❌ 유효하지 않은 번호입니다.")

def toggle_favorite():
    print("\n=== 즐겨찾기 설정/해제 ===")
    show_list()
    num = input("\n프롬프트 번호 선택: ").strip()
    
    if num.isdigit() and 1 <= int(num) <= len(prompts):
        p = prompts[int(num) - 1]
        p["favorite"] = not p["favorite"]
        status = "등록" if p["favorite"] else "해제"
        print(f"\n✅ '{p['title']}' 항목이 즐겨찾기에 {status}되었습니다.")
    else:
        print("❌ 유효하지 않은 번호입니다.")

def show_favorites():
    print("\n=== ⭐ 즐겨찾기 목록 ===")
    favs = [p for p in prompts if p["favorite"]]
    
    if not favs:
        print("즐겨찾기된 프롬프트가 없습니다.")
    else:
        for idx, p in enumerate(favs, 1):
            print(f"{idx}. [{p['category']}] {p['title']} ⭐")
        print(f"\n총 {len(favs)}개의 즐겨찾기")

def main():
    while True:
        show_menu()
        choice = input("선택할 기능의 번호를 입력하세요: ").strip()
        
        if choice == "1":
            add_prompt()
        elif choice == "2":
            show_list()
        elif choice == "3":
            show_by_category()
        elif choice == "4":
            search_prompt()
        elif choice == "5":
            view_detail()
        elif choice == "6":
            toggle_favorite()
        elif choice == "7":
            show_favorites()
        elif choice == "0":
            print("\n프로그램을 종료합니다. 생생정보 포스팅 성공을 기원합니다!")
            break
        else:
            print("\n❌ 잘못된 입력입니다. 다시 선택해 주세요.")

if __name__ == "__main__":
    main()

